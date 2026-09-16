# ChandraMap Dataset Context

This document is the canonical dataset and scientific data-product context for **ChandraMap**.

ChandraMap works with planetary remote-sensing products, not anonymous image files. A raster used by the project may carry scientifically important information about its mission, instrument, physical scale, spectral structure, acquisition geometry, illumination, map projection, provenance, and processing history.

The purpose of this document is to help contributors and AI coding agents answer:

> **What data am I working with, what does it represent, where did it come from, and what assumptions am I allowed to make?**

This document focuses on dataset roles, scientific products, metadata, provenance, reproducibility, benchmark data, and storage expectations.

Detailed scientific interpretation belongs in `DOMAIN_CONTEXT.md`; canonical names belong in `TERMINOLOGY.md`; exact processing behavior belongs in architecture/pipeline documentation; benchmark pair definitions and metric equations belong in benchmark documentation.

---

## 1. Purpose

ChandraMap's data layer should preserve enough scientific context to distinguish between:

* source imagery
* reference imagery
* hyperspectral data
* terrain/elevation products
* benchmark data
* synthetic data
* test fixtures
* derived processing products
* generated experiment artifacts

A file should not be understood only as:

```text
image.tif
```

Scientifically relevant context may include:

* mission
* instrument
* product identifier
* product type
* processing level
* image dimensions
* number of bands
* GSD / pixel scale
* spectral information
* coordinate system
* map projection
* footprint
* acquisition geometry
* illumination geometry
* source/archive
* checksum
* local path
* derived-from relationship
* benchmark role

Preserving provenance and metadata is part of scientific correctness.

---

# 2. Core Dataset Principles

## 2.1 Treat Mission Data as Scientific Products

Mission products are not interchangeable merely because they can be converted into the same raster format.

For example:

```text
OHRC GeoTIFF
```

and:

```text
NAC GeoTIFF
```

may use similar file containers while representing different:

* instruments
* resolutions
* acquisition geometries
* processing histories
* metadata
* scientific meanings

Do not infer scientific equivalence from file extension alone.

---

## 2.2 Product Metadata Overrides Broad Instrument Approximations

Instrument-level values are useful for orientation.

Actual processing should prefer product-specific metadata where available.

For example:

```text
General context:
TMC-2 ≈ 5 m/px

Specific product:
use the actual product pixel scale when available
```

This principle applies to:

* GSD
* projection
* image dimensions
* footprint
* acquisition geometry
* illumination geometry
* spectral definitions

---

## 2.3 Preserve Provenance

Scientifically important inputs should remain traceable to their origin.

Where practical, preserve:

```text
Mission
  ↓
Instrument
  ↓
Product
  ↓
Source / Archive
  ↓
Local Copy
  ↓
Derived Representation
  ↓
Benchmark / Experiment
```

A derived file should not lose its relationship to the source product that created it.

---

## 2.4 Do Not Fabricate Missing Metadata

Never invent:

* GSD
* projection
* footprint
* coordinates
* illumination angles
* viewing geometry
* product level
* band definitions

because an algorithm expects them.

Missing values should remain explicit.

Depending on project design, missing metadata may lead to:

* unknown values
* reduced functionality
* generic processing
* image-only retrieval
* benchmark ineligibility
* input rejection

---

## 2.5 Raw and Derived Data Must Remain Distinct

Derived products such as:

* resized imagery
* PCA components
* edge maps
* tiles
* descriptors
* registered images
* synthetic augmentations

must not be presented as original mission products.

---

# 3. Data Categories

ChandraMap data can be understood through the following conceptual categories.

| Category                       | Purpose                                                          |
| ------------------------------ | ---------------------------------------------------------------- |
| **Primary Source Data**        | Lunar observations being localized or registered                 |
| **Reference Data**             | Imagery used for localization, matching, or registration targets |
| **Terrain / Elevation Data**   | Topographic information supporting geometry or validation        |
| **Optional Research Data**     | Additional datasets evaluated for generalization/research        |
| **Synthetic / Augmented Data** | Derived cases created for controlled experiments                 |
| **Benchmark Data**             | Controlled scientific evaluation inputs                          |
| **Test Fixtures**              | Small deterministic files for automated software testing         |
| **Derived Data**               | Processed representations generated from source/reference data   |
| **Artifacts**                  | Outputs generated by algorithms, experiments, or benchmarks      |

These categories describe scientific roles, not necessarily physical repository directories.

---

# 4. Primary Chandrayaan-2 Data

ChandraMap's primary Chandrayaan-2 context includes three substantially different sensor modalities:

* OHRC
* TMC-2
* IIRS

They must not automatically share identical:

* loaders
* preprocessing
* scale assumptions
* matching representations

---

## 4.1 Chandrayaan-2 OHRC

### Canonical Identity

**Mission:** Chandrayaan-2
**Instrument:** Orbiter High Resolution Camera
**Abbreviation:** OHRC
**Data type:** High-resolution panchromatic/visible lunar imagery
**Typical ChandraMap role:** Primary high-resolution source imagery

### General Spatial Context

Project documentation commonly references OHRC imagery at approximately:

```text
0.25–0.32 m/px
```

depending on the product and authoritative documentation.

This is broad context, not a universal exact constant.

Actual product metadata should govern processing.

### Potential ChandraMap Roles

OHRC may be useful for:

* detailed terrain correspondence
* fine-scale registration
* high-resolution benchmark cases
* local tie-point refinement studies

### Relevant Metadata

Potentially important metadata may include:

* product identifier
* raster dimensions
* GSD / pixel scale
* footprint
* projection
* acquisition time
* illumination geometry
* viewing geometry
* processing state

Only require fields actually defined by the relevant product.

### Registration Considerations

OHRC may contain considerably more spatial detail than coarser Chandrayaan-2 products.

Matching may therefore require:

* scale-aware reference selection
* downsampled reference imagery
* reference pyramids

High spatial resolution does not automatically guarantee easy correspondence.

### Project Status

OHRC is a **primary intended ChandraMap data source**.

Exact end-to-end loader, benchmark, and pipeline support must be determined from the current repository rather than inferred from this document.

---

## 4.2 Chandrayaan-2 TMC-2

### Canonical Identity

**Mission:** Chandrayaan-2
**Instrument:** Terrain Mapping Camera-2
**Canonical abbreviation:** TMC-2
**Data type:** Panchromatic terrain imagery
**Typical ChandraMap role:** Primary source imagery and terrain-scale correspondence

Use:

> **TMC-2**

when specifically referring to the Chandrayaan-2 instrument.

Avoid replacing it with generic `TMC` where instrument identity matters.

### General Spatial Context

TMC-2 imagery is commonly described at approximately:

```text
5 m/px
```

Actual product metadata remains authoritative.

### Potential ChandraMap Roles

TMC-2 may support:

* structural terrain correspondence
* medium-scale registration cases
* scale-stress benchmarks
* terrain-oriented experiments
* comparison against finer reference imagery

### Relevant Metadata

Potentially useful metadata may include:

* product identifier
* image dimensions
* GSD
* footprint
* map projection
* acquisition geometry
* illumination geometry
* processing level/type

Do not assume all TMC-2 products share the same processing state.

### Project Status

TMC-2 is a **primary intended ChandraMap data source**.

Current implementation support must be verified from repository code, tests, and configuration.

---

## 4.3 Chandrayaan-2 IIRS

### Canonical Identity

**Mission:** Chandrayaan-2
**Instrument:** Imaging Infrared Spectrometer
**Abbreviation:** IIRS
**Data type:** Hyperspectral / imaging-infrared data
**Typical ChandraMap role:** Multimodal registration research

IIRS must not be described merely as:

> low-resolution camera

It is a hyperspectral/imaging-infrared scientific instrument.

### General Spatial Context

Project context describes IIRS at approximately:

```text
80 m/px
```

for spatial sampling.

Actual product metadata should govern exact processing.

### Spectral Structure

IIRS products contain many spectral channels.

General project/reference material may describe the number as roughly:

* around 250 bands
* approximately 256 bands

depending on wording and product context.

Do **not** hard-code either value as a universal instrument/product constant.

Use actual product metadata or authoritative product documentation.

### Potential ChandraMap Roles

IIRS may support:

* multimodal registration experiments
* modality-stress benchmarks
* coarse spatial correspondence
* investigation of spectral-to-spatial representations

### Registration Considerations

A conventional 2D feature matcher generally expects a 2D image representation.

A hyperspectral cube may therefore require a derived representation before local matching.

Possible experimental representations include:

* selected spectral band
* selected wavelength region
* PCA component
* multi-band composite
* gradient representation
* edge representation
* structural representation

No representation should be assumed optimal without experimental evidence.

### Project Status

IIRS is a **primary research target / intended multimodal data source**.

Do not describe complete end-to-end support unless current implementation and tests demonstrate it.

---

# 5. IIRS and Hyperspectral Data Handling

## 5.1 Conceptual Data Shape

A conventional grayscale raster may be represented conceptually as:

```text
height × width
```

A hyperspectral product may be represented conceptually as:

```text
height × width × spectral bands
```

The exact storage order is product- and loader-specific.

Do not infer array ordering without inspecting the actual data format.

---

## 5.2 Important Hyperspectral Metadata

Potentially important information includes:

* number of bands
* band ordering
* wavelength definitions
* calibration information
* spatial dimensions
* GSD
* no-data values
* footprint
* projection
* acquisition geometry
* illumination geometry
* processing level

Exact field names belong in product-specific documentation and loaders.

---

## 5.3 Spectral Richness vs Spatial Detail

Many spectral channels do not imply high spatial resolution.

Conceptually:

```text
High spectral information
        ≠
Fine spatial terrain detail
```

This distinction must remain explicit in preprocessing and benchmark interpretation.

---

## 5.4 Derived IIRS Representations

If a 2D representation is generated from IIRS data, record enough provenance to reconstruct it.

Conceptually:

```text
IIRS Source Product
        ↓
Band / PCA / Composite Configuration
        ↓
Derived 2D Representation
        ↓
Registration Experiment
```

The derived representation is not the original IIRS product.

---

# 6. LRO Reference Data

Lunar Reconnaissance Orbiter data may provide important reference imagery.

Use instrument terminology precisely.

---

## 6.1 LRO vs LROC

**LRO**
Lunar Reconnaissance Orbiter mission/spacecraft context.

**LROC**
Lunar Reconnaissance Orbiter Camera system.

Relevant LROC imagery includes:

* NAC
* WAC

Do not use generic wording such as `LRO camera` when the NAC/WAC distinction matters.

---

## 6.2 LRO NAC

### Canonical Identity

**Mission:** Lunar Reconnaissance Orbiter
**Camera system:** LROC
**Instrument class:** Narrow Angle Camera
**Abbreviation:** NAC
**Typical ChandraMap role:** Detailed reference imagery

### General Role

NAC may be useful for:

* high-resolution local reference registration
* source/reference benchmark-pair construction
* detailed correspondence evaluation
* fine-scale registration experiments

### Spatial Context

NAC product scale varies with:

* observation
* geometry
* product processing

Do not hard-code one universal NAC GSD.

### Relevant Metadata

Potentially useful metadata may include:

* product identifier
* raster dimensions
* pixel scale/GSD
* footprint
* map projection
* acquisition geometry
* illumination geometry
* processing state

### Registration Considerations

NAC may contain much more detail than a coarse source image.

A reference pyramid or downsampled representation may therefore be scientifically more appropriate than matching directly against the finest representation.

### Project Status

LRO NAC is an **intended high-resolution reference source**.

Exact current implementation support must be verified from the repository.

---

## 6.3 LRO WAC

### Canonical Identity

**Mission:** Lunar Reconnaissance Orbiter
**Camera system:** LROC
**Instrument class:** Wide Angle Camera
**Abbreviation:** WAC
**Typical ChandraMap role:** Wide-area reference/context imagery

### Potential Roles

WAC may be useful for:

* broad lunar context
* coarse localization
* large-area reference search
* reference database construction
* illumination/context studies

### Important Distinction

NAC and WAC should not be treated as interchangeable.

Conceptually:

```text
NAC
→ finer local reference

WAC
→ broader-area context
```

### Project Status

LRO WAC is an **intended reference/context source**.

Current integration must be verified from implementation evidence.

---

# 7. Reference Image Pyramids

High-resolution reference imagery may need multi-resolution derived representations.

Conceptually:

```text
Original Reference Product
        ↓
Derived Scale Levels
        ↓
Comparable Effective Ground Scale
        ↓
Candidate Search / Matching
```

A reference pyramid is **derived data**.

It is not a separate original mission product.

Each pyramid level should ideally remain traceable to:

* parent reference product
* downsampling/resampling method
* scale level
* processing configuration

Do not present pyramid output as source data.

---

# 8. Terrain and Elevation Data

Terrain/elevation information is different from optical imagery.

Potential supporting datasets may include:

* DEM products
* DTM products
* LOLA-derived elevation products
* mission-derived terrain models

Potential future uses include:

* terrain-aware registration
* orthorectification
* geometry analysis
* relief-aware warping
* validation
* visualization

Do not imply current integration unless repository evidence confirms it.

---

## 8.1 DEM — Digital Elevation Model

A digital representation of terrain elevation.

Potential scientifically relevant properties include:

* horizontal resolution
* vertical reference
* projection
* coverage
* source
* interpolation/generation method

Exact product specifications belong in dataset-specific documentation.

---

## 8.2 DTM — Digital Terrain Model

A terrain elevation representation.

If ChandraMap or a source provider establishes a specific DEM/DTM distinction, preserve that convention.

Do not invent one here.

---

## 8.3 LOLA

**LOLA — Lunar Orbiter Laser Altimeter**

LOLA-derived data may provide lunar elevation/topographic information relevant to:

* terrain modeling
* geometry analysis
* DEM construction/validation
* future terrain-aware registration

LOLA is not an optical image source equivalent to OHRC or NAC.

### Project Status

LOLA-derived products are best treated as a **supporting or research data direction** unless current code establishes active integration.

---

# 9. Optional Cross-Sensor Research Data

## 9.1 Kaguya / SELENE Terrain Camera

Potential research roles include:

* additional lunar cross-sensor registration
* generalization studies
* independent comparison experiments

Treat this as:

> **Optional research data / research candidate**

unless repository implementation explicitly supports it.

---

## 9.2 Additional Lunar Missions

Other lunar datasets may eventually be evaluated where scientifically useful.

Before introducing them, document:

* mission
* instrument
* data modality
* expected spatial scale
* official source
* relevant metadata
* usage/licensing considerations
* benchmark purpose

Do not silently mix a new mission into existing benchmark results.

---

## 9.3 Long-Term Planetary Extensions

Long-term research may eventually consider:

* Mars
* Venus
* other planetary bodies

These are not current core ChandraMap dataset claims.

The project remains centered on lunar imagery unless repository scope explicitly expands.

---

# 10. Synthetic and Augmented Data

Synthetic or augmented data may support controlled experiments.

Potential transformations include:

* rotation
* scaling
* contrast changes
* noise
* geometric perturbation
* limited illumination approximations

---

## 10.1 Synthetic Data Is Not Mission Data

Synthetic output must be clearly identified.

Do not present:

```text
Synthetic Augmentation
```

as:

```text
Original Mission Observation
```

---

## 10.2 Illumination Simulation Caution

Changing image brightness or contrast does not fully simulate real lunar Sun-angle changes.

Real illumination changes may alter:

* shadow direction
* shadow length
* visible slopes
* terrain appearance

Synthetic illumination should therefore be described as an approximation unless a physically grounded simulation is actually used.

---

## 10.3 Synthetic Provenance

Where practical, preserve:

* source item
* transformation type
* transformation parameters
* random seed where relevant
* generated artifact identifier

Conceptually:

```text
Real Source Product
        ↓
Defined Synthetic Transformation
        ↓
Synthetic Derived Item
```

---

# 11. Scientific Product Types

Mission archives can contain products at different processing states.

Exact mission-specific level names must come from authoritative product documentation.

---

## 11.1 Raw Data

Data close to original instrument acquisition.

Do not assume ChandraMap always consumes raw mission products.

---

## 11.2 Calibrated Data

Products that have undergone instrument/radiometric calibration or related scientific processing.

Where suitable standard calibrated products exist, prefer them over reimplementing mission calibration without a clear scientific need.

---

## 11.3 Geometrically Corrected Data

Products that have undergone geometric correction.

Exact correction method and product terminology are provider-specific.

---

## 11.4 Map-Projected Data

Products represented in a defined planetary map projection.

Potential advantages include:

* easier geographic overlap analysis
* known coordinate relationships
* easier reference comparison

Do not assume every input is map-projected.

---

## 11.5 Orthorectified Data

Products corrected using geometric and terrain information to reduce viewing- and relief-related displacement.

Do not assume a product is orthorectified unless its metadata/documentation says so.

---

## 11.6 Browse / Quick-Look Products

Lower-cost representations intended primarily for:

* inspection
* visualization
* search/discovery

Browse imagery may not preserve the same:

* scientific precision
* scale
* bit depth
* metadata
* geometry

as primary science products.

Do not automatically use browse images for final scientific benchmarks.

---

## 11.7 Derived Products

Products generated by ChandraMap or preprocessing workflows.

Examples may include:

* grayscale conversions
* PCA components
* edge maps
* gradient maps
* downsampled imagery
* reference pyramids
* tiles
* descriptors
* registered imagery
* overlays

Derived products must remain traceable to their inputs.

---

# 12. File Formats vs Scientific Products

Do not confuse a **file format** with a **scientific product**.

Example:

```text
GeoTIFF
→ data/container format

OHRC observation
→ mission/instrument product
```

Different mission products may use similar containers but carry different scientific meaning.

Potential upstream or internal formats may include:

* GeoTIFF / TIFF
* PNG
* JPEG
* PDS-related products
* hyperspectral data containers
* label/metadata files
* HDF-style scientific containers
* NumPy arrays
* JSON/YAML manifests
* DEM/DTM raster formats

This list does not imply that every format is currently supported by ChandraMap.

Always distinguish:

```text
Possible upstream format
```

from:

```text
Implemented loader
```

---

# 13. Metadata Requirements

Metadata requirements should be product-aware.

Not every sensor/product must expose every field.

---

## 13.1 Identity Metadata

Potential identity fields include:

* mission
* instrument
* product identifier
* product type
* processing level/type

---

## 13.2 Raster Metadata

Potential raster fields include:

* width
* height
* number of bands
* data type
* no-data definition

---

## 13.3 Spatial Metadata

Potential fields include:

* GSD / pixel scale
* projection
* CRS / planetary reference
* footprint
* geotransform
* spatial extent

---

## 13.4 Acquisition Metadata

Potential fields include:

* acquisition timestamp
* spacecraft geometry
* viewing geometry

---

## 13.5 Illumination Metadata

Where provided, relevant fields may include:

* solar incidence
* solar azimuth
* illumination direction
* other provider-defined solar geometry

Do not assume every mission product exposes identical fields.

---

## 13.6 Spectral Metadata

For IIRS or other multi-band data, relevant information may include:

* band count
* band ordering
* wavelength definitions
* calibration information
* no-data values

Exact metadata names and structure belong in dataset/product documentation.

---

# 14. Product Metadata vs Instrument-Level Values

Broad instrument values are useful for orientation.

They should not override product-level information.

For example:

```text
Documentation:
TMC-2 ≈ 5 m/px

Product metadata:
specific pixel scale
```

Use the specific product value during processing when it is available and trustworthy.

This applies equally to OHRC, IIRS, NAC, WAC, and derived terrain products.

---

# 15. Planetary Geospatial Metadata

## 15.1 Lunar CRS

Do not automatically assign terrestrial CRS definitions to lunar products.

In particular, do not silently assume:

```text
WGS84
EPSG:4326
```

for lunar coordinates.

Preserve actual provider/project information about:

* lunar body reference
* projection
* coordinate convention
* longitude convention

---

## 15.2 Projection

Projection metadata should remain attached to or traceable from relevant map-projected products.

Do not fabricate projection information for unprojected data.

---

## 15.3 Longitude Convention

Planetary products may differ in longitude representation.

Possible conventions may include:

* positive-east
* positive-west
* `0°–360°`
* `-180°–+180°`

Do not silently normalize between conventions.

Conversions should be:

* explicit
* documented
* reproducible

---

## 15.4 Footprint

A footprint represents the lunar surface region covered by a product.

It is more scientifically useful for overlap analysis than raster dimensions alone.

---

## 15.5 Bounding Box

A rectangular coordinate range surrounding or approximating a region.

A bounding box is not necessarily identical to the exact image footprint.

---

## 15.6 GSD / Pixel Scale

Units must remain explicit.

Prefer:

```text
5 m/px
```

over an unlabeled value such as:

```text
pixel_size = 5
```

unless the schema defines the unit elsewhere.

---

# 16. Image Dimensions

Preserve raster dimensions explicitly.

Where applicable, distinguish:

```text
width × height
```

from array indexing such as:

```text
row × column
```

Image dimensions do not determine physical scale.

A `4096 × 4096` image may represent a vastly different lunar area from another `4096 × 4096` image.

---

# 17. Multi-Band Data

For multi-band products, preserve where available:

* number of bands
* band ordering
* wavelength information
* calibration information
* masks
* no-data information
* derived-band provenance

Do not arbitrarily reorder or discard bands without recording that operation.

---

# 18. No-Data and Invalid Pixels

Scientific rasters may contain:

* no-data values
* masked pixels
* invalid samples
* saturated values
* missing scan regions

These should not automatically be interpreted as real terrain measurements.

Do not invent one universal no-data value.

Loader behavior should preserve validity information where relevant.

---

# 19. Metadata-First Search

When trustworthy location information exists, it can be used to narrow reference search.

Conceptually:

```text
Source Footprint / Coordinates
        ↓
Candidate Reference Region
        ↓
Local Registration
```

Metadata-constrained search is scientifically valid and often preferable to unnecessary whole-Moon image retrieval.

---

# 20. Missing Metadata

Missing metadata must remain explicit.

Do not fabricate:

* coordinates
* footprint
* GSD
* projection
* illumination values

Possible handling may include:

* record as unknown
* use a documented instrument-level approximation for non-critical operations
* disable functions requiring exact values
* use image-only retrieval
* reject benchmark eligibility

Exact behavior belongs in architecture/configuration documentation.

---

# 21. Data Provenance

Scientifically significant dataset items should remain traceable where practical.

Potential provenance fields include:

* mission
* instrument
* product ID
* product type
* processing state
* authoritative source/archive
* original filename
* acquisition date
* download/retrieval date
* checksum
* GSD
* projection
* footprint
* parent product
* derivation method

Not every upstream source supplies every field.

Do not invent missing provenance.

---

# 22. Dataset Identifiers

Prefer stable identifiers where available.

Useful identifiers may include:

* official product identifier
* archive product name
* repository-defined dataset item ID

Do not rely solely on ambiguous local filenames such as:

```text
image1.tif
test2.png
final.tif
```

as scientific identity.

---

# 23. Local Filenames

A filename is not sufficient metadata.

For example:

```text
image1.tif
```

does not reveal:

* mission
* sensor
* product identifier
* GSD
* projection
* footprint
* illumination geometry

Use manifests or metadata rather than encoding all scientific meaning implicitly in filenames.

---

# 24. Dataset Manifests

Machine-readable manifests are preferred for reproducible data selection.

A manifest may conceptually describe:

* dataset/item identity
* mission
* instrument
* source
* local file reference
* checksum
* product identifier
* dimensions
* GSD
* projection
* footprint
* benchmark split/category
* notes

This is conceptual only.

Do not invent a concrete manifest schema if the repository already defines one.

If a schema exists in repository contracts, configuration, benchmark definitions, or data tooling, that schema is authoritative.

---

## 24.1 Manifest Goals

A useful manifest should make it possible to answer:

* Which product was used?
* Where did it come from?
* Which local file represents it?
* Which benchmark/experiment used it?
* Can the exact dataset selection be reconstructed?

---

# 25. Checksums

Checksums may support:

* file integrity
* dataset identity
* reproducibility
* corruption detection

Do not mandate a particular hash algorithm unless project policy defines one.

A checksum is supplemental provenance; it does not replace official product identity.

---

# 26. Dataset Validation

Data ingestion should validate properties relevant to the product.

Potential checks include:

* file exists
* file is readable
* dimensions are valid
* band structure is plausible for the declared product
* metadata is parseable
* required companion files exist
* GSD is valid where required
* projection exists where required
* footprint is valid where required
* sensor identity is recognized
* checksum matches where a manifest defines one

Validation must be sensor/product-aware.

Do not require every field for every dataset.

---

## 26.1 Conceptual Validation Levels

Validation may conceptually occur at several levels:

1. **File validation**
2. **Metadata validation**
3. **Sensor/product validation**
4. **Geospatial validation**
5. **Benchmark eligibility validation**

These are conceptual layers, not required class names or architecture.

---

# 27. Dataset Quality Control

Potential QC concerns include:

* corrupt files
* unreadable metadata
* invalid dimensions
* invalid footprints
* inconsistent CRS
* impossible/invalid scale values
* duplicate products
* no-data-dominated imagery
* incomplete hyperspectral cubes
* missing companion metadata

Do not invent universal QC thresholds.

Thresholds should be defined by product-specific validation or benchmark rules.

---

# 28. Benchmark Eligibility

A readable raster should not automatically qualify as benchmark data.

Benchmark eligibility may require:

* known mission/instrument
* stable product identity
* sufficient metadata
* known source/reference relationship
* documented overlap
* evaluation reference
* valid provenance
* suitable licensing/usage status
* reproducible preprocessing

Exact benchmark eligibility criteria belong in benchmark documentation.

---

# 29. Benchmark Data

Benchmark datasets should preserve enough context to reproduce comparisons.

Relevant information may include:

* source item
* reference item
* source/reference sensor pair
* region/overlap
* stress category
* ground truth/check points where available
* preprocessing/configuration reference
* dataset split
* benchmark version

Do not represent a scientific benchmark only as:

```text
source.png
reference.png
```

without the surrounding data context.

---

# 30. Benchmark Pairs

A benchmark pair conceptually contains:

```text
Source Product
      +
Reference Product / Tile
      +
Relevant Metadata
      +
Evaluation Reference
```

Potential stress categories may include:

* easy
* Sun-angle stress
* scale stress
* modality stress
* geometry stress
* low-feature terrain
* retrieval stress

Do not invent pair counts or claim all categories currently exist.

---

# 31. Ground Truth

Ground truth should come from a documented and sufficiently trustworthy evaluation source.

Potential forms may include:

* challenge-provided reference information
* independently verified check points
* trusted geospatial reference
* carefully validated manual correspondences

Automatically generated matcher output is not ground truth.

The source and method used to construct ground truth must be documented.

---

## 31.1 Manual Annotations

If manual tie/check points are created, preserve where practical:

* image/product identifiers
* coordinates
* coordinate convention
* units
* annotation procedure
* annotation version
* quality notes

Do not invent an annotation framework if none exists.

---

# 32. Benchmark Dataset Changes

Changes to benchmark data can invalidate historical comparisons.

Potential comparison-breaking changes include:

* adding pairs
* removing pairs
* replacing source/reference products
* modifying ground truth
* modifying check points
* changing labels/categories
* changing preprocessing

When benchmark data changes, document the change.

Do not compare benchmark averages before and after a dataset change as though only the algorithm changed.

---

# 33. Train / Validation / Test Data

If ChandraMap introduces machine-learning training, data splits may require:

* train
* validation
* test

Split membership should be reproducible.

Do not generate new random splits on every run without recording the split definition or seed.

---

## 33.1 Geographic Leakage

Random image-level splitting can create misleading ML evaluation when crops or observations from the same lunar region appear in both training and evaluation sets.

Future ML dataset design should consider separation by:

* geographic region
* observation
* sensor
* illumination condition

where scientifically appropriate.

Do not claim a particular split strategy currently exists unless repository evidence confirms it.

---

# 34. Duplicate and Related Data

Potential related/duplicate data may include:

* exact duplicate file
* re-encoded version
* crop from the same observation
* alternative pyramid level
* preprocessed version
* registered version
* synthetic augmentation

These are not automatically errors.

Their relationships should be clear in provenance so they are not incorrectly treated as independent samples.

---

# 35. Test Fixtures

Test fixtures are small, deterministic inputs used for software tests.

Fixtures should ideally be:

* small
* stable
* documented
* representative enough for their test purpose
* legally redistributable

Do not commit full mission-scale products merely to simplify unit testing.

---

# 36. Test Data vs Benchmark Data vs Research Data

Keep these categories distinct.

| Category                  | Purpose                                      |
| ------------------------- | -------------------------------------------- |
| **Test Fixture**          | Deterministic software correctness testing   |
| **Benchmark Data**        | Controlled scientific method comparison      |
| **Research Data**         | Exploratory experiment input                 |
| **User/Operational Data** | Data processed during actual use             |
| **Synthetic Data**        | Generated/augmented research or testing data |

Do not automatically reuse one category as another without documenting the implications.

---

# 37. Large-File Policy

Planetary mission data can be very large.

Do not casually commit full raw mission datasets into ordinary Git history.

Prefer mechanisms such as:

* manifests
* download/acquisition documentation
* checksums
* local caches
* small fixtures
* configurable external dataset locations

Large files in normal Git history can permanently increase repository size and cloning cost.

---

## 37.1 Git LFS

Do not assume Git LFS is automatically the correct solution for every scientific dataset.

If the repository explicitly adopts Git LFS for particular assets, document that policy separately.

Large upstream mission archives may still be better managed outside the source repository.

---

# 38. Raw Data Preservation

Where practical, locally acquired original mission products should remain immutable.

Prefer:

```text
Original Product
      ↓
Read-Only Source
      ↓
Derived Processing Output
```

instead of modifying the original scientific file in place.

Benefits include:

* reproducibility
* easier provenance tracking
* easier debugging
* safer reprocessing

---

# 39. Derived Data

Potential derived data includes:

* grayscale conversions
* selected-band images
* PCA components
* composites
* edge maps
* gradient maps
* downsampled imagery
* reference pyramids
* tiles
* descriptors
* vector embeddings
* FAISS indexes
* registered images
* overlays
* masks

Derived data should ideally remain traceable to:

* source product
* processing operation
* configuration
* relevant model/method
* scale level
* code/version context where available

---

## 39.1 Derived Data Is Not Source Data

Never label:

```text
PCA component
Downsampled NAC tile
Synthetic augmentation
Registered output
```

as an original mission product.

---

# 40. Data Transformation Provenance

Important transformations should be reproducible.

Example:

```text
IIRS Cube
    ↓
PCA Configuration
    ↓
Selected Component
    ↓
Registration Representation
```

Another example:

```text
NAC Product
    ↓
Defined Downsampling
    ↓
Reference Pyramid Level
```

Record enough context to understand how the derived representation was created.

---

# 41. Retrieval Reference Data

A future or implemented retrieval database may contain several distinct levels:

```text
Reference Products
        ↓
Reference Tiles
        ↓
Multi-Scale Tiles
        ↓
Global Descriptors
        ↓
Vector Index
```

These must not be treated as the same thing.

---

## 41.1 Reference Tiles

A reference tile is a spatial subset derived from a larger reference product.

Relevant provenance may include:

* parent product
* tile identifier
* footprint/location
* pyramid level
* effective scale

Do not invent tile dimensions or naming conventions.

---

## 41.2 Tile Pyramids

Multi-scale tile sets are derived from reference data.

Each level should ideally preserve:

* parent relationship
* scale level
* spatial relationship
* generation configuration

---

## 41.3 Global Descriptors

A global descriptor is derived retrieval data.

It should remain traceable to:

* source/reference tile
* descriptor method/model
* preprocessing
* scale level
* descriptor version/configuration

Global descriptors are not original imagery.

---

## 41.4 FAISS Indexes

If FAISS is used, its index is a generated retrieval artifact.

Conceptually:

```text
Reference Tiles
      ↓
Global Descriptors
      ↓
FAISS Index
```

The FAISS index is **not** the authoritative lunar dataset.

The authoritative scientific source remains:

```text
Mission Product
      +
Metadata
      +
Descriptor Generation Configuration
```

Indexes should be reproducible where practical.

---

# 42. Dataset vs Artifact

## Dataset

An input/reference collection used for:

* processing
* training
* experimentation
* benchmarking
* evaluation

## Artifact

An output generated by ChandraMap.

Potential artifacts include:

* registered images
* match visualizations
* overlays
* metrics
* benchmark reports
* descriptors
* indexes
* embeddings

Generated artifacts must not be confused with original mission datasets.

---

# 43. Local Cache

Mission products may be cached locally.

A cache policy may need to consider:

* disk usage
* integrity
* checksums
* invalidation
* source changes
* reproducibility

Do not assume a cache path or implementation that is not defined by the repository.

---

# 44. Dataset Paths

Dataset locations should generally be configurable rather than hard-coded in source code.

Possible mechanisms may include:

* configuration files
* manifest roots
* environment-specific paths

Use actual repository conventions.

Do not invent configuration keys.

---

## 44.1 Path Portability

Avoid embedding developer-specific absolute paths in portable manifests where unnecessary.

Examples to avoid as repository-level assumptions:

```text
C:\Users\name\...
/home/name/...
```

Prefer portable/configurable references where possible.

Do not sacrifice scientific provenance merely to force every path to be relative.

---

# 45. Dataset Loader Responsibilities

Conceptually, a loader may be responsible for:

* locating data files
* reading raster/spectral arrays
* reading metadata
* validating file structure
* preserving masks
* preserving band relationships
* creating appropriate internal representations

A loader should not silently:

* fabricate GSD
* fabricate CRS
* invent sensor identity
* discard scientifically important bands
* modify scientific values without documentation
* overwrite source files

Exact loader architecture belongs elsewhere.

---

# 46. Sensor Routing

Sensor identity may determine preprocessing behavior.

Conceptually:

```text
Input Product
      ↓
Identify Instrument
      ↓
┌────────┬─────────┬────────┐
│ OHRC   │ TMC-2   │ IIRS   │
└────────┴─────────┴────────┘
      ↓
Sensor-appropriate handling
```

Use reliable product metadata for sensor identification when available.

Do not infer instrument identity from appearance alone when authoritative metadata exists.

---

# 47. Unknown Sensor

If the instrument cannot be identified, do not silently assume:

* OHRC
* TMC-2
* IIRS

Possible project behavior may include:

* explicit user declaration
* unknown-sensor status
* generic preprocessing
* input rejection

Actual behavior must come from architecture/configuration.

---

# 48. Metadata Normalization

Different providers may use different metadata field names and structures.

A common internal metadata representation may eventually normalize these differences.

If normalization is implemented:

* preserve original scientific meaning
* retain provider-specific metadata where useful
* record conversions where relevant

Normalization should not silently rewrite scientific facts.

---

# 49. No Silent Data Correction

If metadata appears inconsistent:

do not silently replace it.

Prefer an auditable process such as:

```text
Original Metadata
      ↓
Detected Issue
      ↓
Documented Correction / Derived Value
      ↓
Corrected Internal Representation
```

Where practical:

* retain original value
* document the reason for correction
* identify the source of the replacement/correction

---

# 50. Source and Reference Roles

A product's role is contextual.

The same dataset could potentially act as:

* source in one experiment
* reference in another

depending on benchmark design.

This document describes typical roles, not permanent physical restrictions.

---

# 51. Pair Manifests

Controlled registration benchmarks may benefit from explicit pair manifests.

A pair definition may conceptually include:

* pair identifier
* source item
* reference item
* expected/known overlap
* stress category
* evaluation reference/check points
* notes

Do not invent exact fields when a repository schema already exists.

---

# 52. Download and Acquisition Sources

Dataset acquisition should prioritize authoritative providers.

Potential provider contexts include:

### Chandrayaan-2

Official ISRO / ISSDC / PRADAN data infrastructure.

### LRO / LROC

Official NASA / PDS / LROC data infrastructure.

### Kaguya / SELENE

Official JAXA or other authoritative SELENE data archives where relevant.

This document does not provide download URLs because URLs should be verified before inclusion.

Detailed:

* commands
* login procedures
* APIs
* download scripts

belong in dedicated acquisition documentation or repository scripts.

---

# 53. Official Sources First

Prefer authoritative mission or agency sources for scientific data.

Avoid relying on random file-sharing mirrors for benchmark-critical products.

If an unofficial mirror is ever used, preserve enough provenance to identify the original authoritative product.

---

# 54. External Source Stability

Archive layouts and URLs may change.

Therefore provenance should prefer stable information such as:

* mission
* instrument
* product ID
* archive identifier

rather than relying only on one transient URL.

---

# 55. Licensing, Redistribution, and Usage

Do not invent legal terms for mission data.

Different providers or products may have different:

* usage conditions
* attribution requirements
* redistribution rules

Before redistributing data in the ChandraMap repository or releases:

* check the official provider policy
* preserve required attribution
* avoid unsupported legal conclusions

This file provides data-governance guidance, not legal advice.

---

# 56. Attribution

Data-provider attribution should be preserved where required.

Relevant organizations may include:

* ISRO
* ISSDC / PRADAN
* NASA
* LROC
* JAXA / SELENE data providers

Data attribution is different from software authorship.

Using an agency's data does not make the agency an author of ChandraMap software.

---

# 57. Private and User-Provided Data

If ChandraMap accepts user-provided imagery in future or current deployments:

* do not automatically publish it
* do not expose developer-local paths
* do not expose private metadata unintentionally
* follow applicable deployment/security policies

Generated artifacts should inherit appropriate privacy handling.

---

# 58. Untrusted Files

External scientific data should be treated as potentially untrusted at the software boundary.

Possible risks include:

* malformed raster files
* corrupted metadata
* extremely large images
* decompression bombs
* unsafe archives
* parser bugs
* unsafe serialized objects

Detailed mitigations belong in engineering/security documentation.

---

# 59. Resource Scale

Planetary datasets can become large in:

* disk usage
* memory usage
* tile counts
* pyramid levels
* temporary files
* descriptor/index size

Do not assume an entire lunar reference collection can be loaded into memory at once.

Large-data workflows may require:

* tiling
* chunking
* streaming
* caching
* memory mapping

where architecture justifies them.

---

# 60. No Automatic Full-Moon Requirement

Basic development and unit testing should not require downloading an entire global lunar reference dataset.

Prefer:

* compact fixtures
* selected benchmark regions
* small example products
* configurable external data

Whole-Moon retrieval infrastructure should remain a distinct large-data workflow.

---

# 61. Dataset Versioning

Dataset versions may need to track changes to:

* item membership
* ground truth
* annotations
* preprocessing assumptions
* train/validation/test splits
* manifests

Do not invent a versioning system unless one already exists.

At minimum, benchmark data selections should be reconstructable historically where practical.

---

# 62. Synthetic Augmentation Reproducibility

When synthetic data affects experiments, preserve enough context to recreate it.

Potential information includes:

* source item
* augmentation type
* parameters
* random seed
* generated identifier

Do not allow synthetic benchmark cases to become disconnected from their source.

---

# 63. Dataset Support Status

Use support terminology conservatively.

## Supported

Current repository code and documentation intentionally support the dataset/product.

Do not apply this status without implementation evidence.

## Experimental

The dataset/product is actively being investigated but may not work reliably.

## Planned

Support is intended but not yet implemented.

## Research Candidate

The dataset may be scientifically useful but is not necessarily part of an active implementation plan.

## Reference Only

The dataset is documented or used as conceptual/reference material but is not integrated into normal processing.

## Deprecated

Previously supported path intended for removal or replacement.

---

# 64. High-Level Dataset Role Matrix

Implementation status has not been established by this context document, so the matrix below describes **project role**, not code-level support.

| Dataset / Instrument    | Typical Role            | Data Type                  | Project Context                     | Important Note                                      |
| ----------------------- | ----------------------- | -------------------------- | ----------------------------------- | --------------------------------------------------- |
| Chandrayaan-2 OHRC      | Primary source          | Panchromatic               | Intended primary data source        | Fine-scale lunar imagery                            |
| Chandrayaan-2 TMC-2     | Primary source          | Panchromatic               | Intended primary data source        | Terrain-scale correspondence                        |
| Chandrayaan-2 IIRS      | Primary research source | Hyperspectral / imaging IR | Intended multimodal research target | Requires appropriate 2D registration representation |
| LRO NAC                 | Reference               | High-resolution optical    | Intended reference source           | Product scale varies                                |
| LRO WAC                 | Reference / context     | Wide-area optical          | Intended context/reference source   | Broad coverage                                      |
| DEM / DTM               | Supporting              | Elevation / terrain        | Research/supporting data            | Not equivalent to optical imagery                   |
| LOLA-derived products   | Supporting              | Topographic/elevation      | Research/supporting data            | Terrain information                                 |
| Kaguya / SELENE TC      | Research                | Optical                    | Optional research candidate         | Do not assume current support                       |
| Synthetic augmentations | Research / benchmark    | Derived                    | Optional experimental data          | Must retain provenance                              |

Do not interpret `Intended` or `Research` as `Supported`.

---

# 65. Adding a New Dataset

Before adding a new dataset or instrument, document:

1. scientific purpose
2. mission/provider
3. instrument
4. data modality
5. approximate spatial characteristics
6. spectral characteristics where relevant
7. authoritative source
8. product/format context
9. metadata availability
10. projection/geospatial context
11. licensing/usage considerations
12. expected ChandraMap role
13. benchmark impact
14. implementation/support status

Do not add new datasets simply because they are available.

---

# 66. Removing Dataset Support

Before removing or changing dataset support, inspect impact on:

* loaders
* manifests
* tests
* configurations
* benchmark pairs
* experiments
* documentation
* cached derived data
* historical results

Do not silently invalidate previous benchmark configurations.

---

# 67. Maintaining This Document

Update `DATASETS.md` when changes materially affect:

* mission/instrument coverage
* product types
* dataset roles
* loaders
* metadata requirements
* provenance policy
* benchmark datasets
* manifest design
* storage policy
* redistribution constraints
* terrain/elevation inputs
* synthetic-data policy

Do not update this file for every individual downloaded product.

---

# 68. Relationship with Other Context Documents

`PROJECT_CONTEXT.md`
→ explains what ChandraMap is and why these datasets matter.

`DOMAIN_CONTEXT.md`
→ explains scientific consequences of sensor, scale, illumination, and geometry differences.

`TERMINOLOGY.md`
→ defines canonical mission, sensor, metric, and data terms.

`DATASETS.md`
→ defines dataset roles, products, metadata, provenance, and handling expectations.

Architecture/pipeline documentation
→ defines how ChandraMap reads and processes the data.

Benchmark documentation
→ defines exact benchmark items, pairs, splits, and evaluation conditions.

Engineering/security documentation
→ defines loader implementation, validation, storage, and secure file handling.

Do not duplicate the full responsibility of those documents here.

---

# 69. Key Dataset Rules for AI Agents

1. Treat mission files as **scientific products**, not anonymous images.

2. Preserve mission, instrument, product identity, and provenance where available.

3. OHRC, TMC-2, and IIRS are different sensor/data modalities.

4. Use **TMC-2**, not generic `TMC`, when referring specifically to the Chandrayaan-2 instrument.

5. Treat IIRS as **hyperspectral / imaging-infrared data**.

6. Do not hard-code a universal IIRS band count when product metadata should determine it.

7. Use product metadata for exact GSD and geometry whenever available.

8. Do not infer physical scale from raster dimensions alone.

9. Do not fabricate missing projection, GSD, footprint, spectral, or illumination metadata.

10. Do not automatically assign Earth CRS definitions such as WGS84 to lunar data.

11. Preserve projection, body-reference, and longitude conventions explicitly where available.

12. Distinguish mission products from derived processing output.

13. Upsampled, downsampled, PCA-derived, composite, edge, and gradient images are **derived data**.

14. Reference pyramids and tiles are derived representations of reference products.

15. Keep original/raw downloaded scientific products unchanged where practical.

16. Do not casually commit large mission datasets into ordinary Git history.

17. Use manifests and checksums where appropriate to improve reproducibility.

18. Do not treat browse/quick-look imagery as automatically suitable for scientific benchmarking.

19. Do not call automatically generated matcher correspondences ground truth.

20. Ground-truth/check-point origin must be documented.

21. Benchmark dataset changes can break historical comparability and must be documented.

22. Synthetic data must be clearly identified as synthetic or derived.

23. Synthetic brightness changes do not fully reproduce real lunar Sun-angle geometry.

24. Do not mix synthetic and real data without preserving provenance.

25. Test fixtures, benchmark data, research data, and operational/user data are different categories.

26. Do not assume every possible upstream file format has an implemented ChandraMap loader.

27. Do not mark a dataset **Supported** without repository evidence.

28. Reference tiles, descriptors, embeddings, and FAISS indexes are generated data/artifacts, not original mission products.

29. A FAISS index is not the lunar reference dataset itself.

30. Metadata-constrained search is valid when reliable location information exists.

31. Unknown metadata should remain unknown rather than being fabricated.

32. Preserve no-data/mask information where it affects scientific interpretation.

33. Do not silently reorder hyperspectral bands.

34. Do not silently correct scientific metadata without retaining an auditable explanation.

35. Prefer stable product identifiers over ambiguous local filenames.

36. Avoid developer-specific absolute paths in portable manifests when practical.

37. Dataset loaders should preserve scientific meaning rather than silently normalizing it away.

38. Train/test design for future ML work must consider geographic leakage.

39. Whole-Moon datasets should not be required for ordinary unit tests.

40. Data-provider licensing, usage, attribution, and redistribution requirements must be verified from authoritative sources before redistribution.

41. Official mission/provider sources should be preferred for benchmark-critical scientific data.

42. Every important benchmark should be reconstructable from documented data identities, metadata, and configuration where practical.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
