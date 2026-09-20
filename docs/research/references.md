# ChandraMap Research References

This document is the canonical research-reference index for ChandraMap. It collects authoritative mission documentation, data archives, planetary-processing resources, primary computer-vision papers, multimodal remote-sensing methods, and software references that directly support ChandraMap's work on lunar image correspondence and registration.

The purpose of this file is **source discovery and traceability**, not literature review.

> **Mission and sensor facts should come from official mission sources before secondary summaries.**

> **Product-specific metadata is more authoritative for an experiment than a generic instrument-level specification.**

> **A method being listed here does not imply that ChandraMap currently implements it or that it performs better on lunar imagery.**

---

## 🎯 Purpose

Use this reference index when you need authoritative material for:

- Chandrayaan-2 mission and payloads
- OHRC
- TMC-2
- IIRS
- Chandrayaan-2 science-data access
- LRO and LROC
- NAC and WAC products
- NASA Planetary Data System data
- planetary image processing
- lunar map projection
- image-to-image registration
- planetary control networks
- SIFT and RootSIFT
- descriptor matching
- RANSAC
- affine/projective geometry
- homography estimation
- sub-pixel registration
- learned local correspondence
- ALIKED
- LightGlue
- LoFTR
- multimodal remote-sensing registration
- RIFT
- CFOG
- vector retrieval and FAISS
- benchmark/evaluation methodology
- reproducibility and scientific citation

This document should remain curated. A reference belongs here only when it directly supports a ChandraMap scientific, engineering, evaluation, or data-access need.

---

## 🧭 Reference Policy

ChandraMap should prefer references in approximately this order:

1. official mission or space-agency documentation
2. official science-data archives
3. official instrument/product documentation
4. official planetary-processing documentation
5. primary peer-reviewed papers
6. official repositories maintained by method authors
7. official software documentation
8. established secondary technical resources when no stronger source is available

Avoid using:

- random blogs
- SEO articles
- unofficial mirrors
- scraped documentation
- unverified forks
- AI-generated summaries
- social-media posts as primary scientific evidence

### Source-of-truth hierarchy

| Claim Type                                 | Preferred Source                          |
| ------------------------------------------ | ----------------------------------------- |
| Mission facts                              | ISRO, NASA, JAXA, mission teams           |
| Sensor specifications                      | Official payload/instrument documentation |
| Product-specific GSD, projection, geometry | Actual product metadata/documentation     |
| Chandrayaan-2 data access                  | ISSDC / PRADAN                            |
| LRO/LROC data access                       | LROC / NASA PDS                           |
| Planetary processing                       | USGS ISIS documentation                   |
| Algorithm definition                       | Original research paper                   |
| Algorithm implementation                   | Official author repository                |
| Library behavior                           | Official documentation/repository         |
| ChandraMap benchmark result                | Reproducible ChandraMap result record     |

---

## ⭐ Core References

These are the highest-priority external references for understanding the current ChandraMap research problem.

| Reference                                      | Type                                | Area                       | Why It Matters                                                     |
| ---------------------------------------------- | ----------------------------------- | -------------------------- | ------------------------------------------------------------------ |
| ISRO Chandrayaan-2 Payloads                    | Official Mission Documentation      | Sensors                    | Authoritative overview of OHRC, TMC-2, IIRS, and other payloads    |
| Chandrayaan-2 Payloads Data & Science Handbook | Official Mission Documentation      | Sensors / Products         | Detailed instrument and product context                            |
| PRADAN / ISDA                                  | Official Data Archive               | Chandrayaan-2              | Product discovery and science-data access                          |
| LROC Instrument Overview                       | Primary Mission Paper               | NAC / WAC                  | Instrument architecture and nominal camera characteristics         |
| LROC NAC Processing Guide                      | Official Processing Guide           | NAC / Planetary Processing | NAC calibration, projection, and ISIS workflow                     |
| NASA PDS LROC Archive                          | Official Data Archive               | LRO                        | LROC NAC/WAC products and documentation                            |
| USGS ISIS                                      | Official Software Documentation     | Planetary Processing       | Projection, registration, control networks, and planetary geometry |
| Lowe SIFT                                      | Primary Research Paper              | Classical Matching         | Classical local-feature baseline                                   |
| Fischler & Bolles RANSAC                       | Primary Research Paper              | Geometric Verification     | Robust model estimation                                            |
| LightGlue                                      | Primary Paper / Official Repository | Learned Sparse Matching    | Candidate learned sparse matcher                                   |
| LoFTR                                          | Primary Research Paper              | Detector-Free Matching     | Candidate detector-free matcher                                    |
| RIFT                                           | Primary Research Paper              | Multimodal Matching        | Multimodal structural correspondence research                      |
| CFOG                                           | Primary Research Paper              | Multimodal Matching        | Structural multimodal remote-sensing registration                  |
| FAISS                                          | Official Repository                 | Retrieval                  | Dense-vector similarity indexing/search                            |

---

# 🇮🇳 Chandrayaan-2 Mission and Data

## Chandrayaan-2 Payloads

**Organization:** Indian Space Research Organisation (ISRO)
**Type:** Official Mission Documentation
**Reference Status:** Core
**Used for:** Mission payload descriptions and high-level instrument characteristics.

The official payload page describes TMC-2 as a panchromatic terrain-mapping camera, OHRC as a high-resolution imaging system, and IIRS as an imaging infrared spectrometer. ([ISRO][1])

[ISRO — Chandrayaan-2 Payloads](https://www.isro.gov.in/chandrayaan2-payloads.html)

---

## Chandrayaan-2 Science Overview

**Organization:** ISRO
**Type:** Official Mission Documentation
**Reference Status:** Core
**Used for:** Instrument scientific objectives, TMC-2 mapping context, and OHRC scientific use.

ISRO's science page documents TMC-2 at approximately 5 m spatial resolution with a 20 km swath and describes OHRC's high-resolution imaging and DEM-related roles. ([ISRO][2])

[ISRO — Chandrayaan-2 Science](https://www.isro.gov.in/Chandrayaan2_science.html)

---

## Chandrayaan-2 Science and Data Product Documents

**Organization:** ISRO
**Type:** Official Mission / Product Documentation
**Reference Status:** Core
**Used for:** Access to the payload handbook, science-results documentation, and orbiter data-product documentation.

ISRO provides a dedicated science/data-products page containing the _Handbook of Chandrayaan-2 Payloads Data and Science_, mission science results, and orbiter payload/data-product documentation. ([ISRO][3])

[ISRO — Chandrayaan-2 Science and Data Product Documents](https://www.isro.gov.in/ScienceandDataProduct.html)

---

## Handbook of Chandrayaan-2 Payloads, Data and Science

**Organization:** ISRO
**Type:** Official Instrument / Product Documentation
**Year:** 2021
**Reference Status:** Core
**Used for:** Detailed OHRC, TMC-2, IIRS, product, science, and sensor specifications.

The handbook documents TMC-2 at approximately 5 m spatial resolution and describes IIRS as a hyperspectral optical imaging instrument covering approximately 0.8–5.0 µm with about 80 m spatial sampling and 256 contiguous spectral bands in the cited mission-level description. ([ISRO][4])

[ISRO — Handbook of Chandrayaan-2 Payloads, Data and Science](https://www.isro.gov.in/media_isro/pdf/science/hand_book_payloads_data_and_science.pdf)

---

# 🗃️ Chandrayaan-2 Data Access

## PRADAN / ISRO Science Data Archive

**Organization:** Indian Space Science Data Centre (ISSDC) / ISRO
**Type:** Official Data Archive
**Reference Status:** Core
**Used for:** Chandrayaan-2 product search, browsing, metadata inspection, and data access.

PRADAN is the ISSDC-hosted science-data system through which Chandrayaan-2 payload products are made available. Its Chandrayaan-2 interface includes OHRC, TMC-2, IIRS, and other orbiter payload data. ([Pradan][5])

[PRADAN — ISRO Science Data Archive](https://pradan.issdc.gov.in/)

[PRADAN — Chandrayaan-2 Archive](https://pradan.issdc.gov.in/ch2/)

> Product availability, product level, metadata content, projection state, and GSD should be inspected from the actual products used in each experiment.

---

# 📷 OHRC

## Orbiter High Resolution Camera — Official Payload Documentation

**Organization:** ISRO
**Type:** Official Instrument Documentation
**Reference Status:** Core
**Used for:** OHRC instrument type, imaging role, coverage, and nominal spatial-resolution context.

Different official ISRO documents describe OHRC using approximately 0.25 m and 0.32 m figures in different mission/product contexts. ISRO's detailed science-results document gives a 0.25 m nadir GSD, while another official payload description gives 0.32 m ground resolution. ChandraMap therefore uses approximately **0.25–0.32 m/pixel** only as instrument-level context and relies on actual product metadata for experiments. ([ISRO][6])

[ISRO — Chandrayaan-2 Science / OHRC Context](https://www.isro.gov.in/Chandrayaan2_science.html)

[ISRO — Chandrayaan-2 Payloads](https://www.isro.gov.in/chandrayaan2-payloads.html)

### Resolution caution

Do not write:

```text
OHRC GSD = exactly 0.25 m for every product
```

or:

```text
OHRC GSD = exactly 0.32 m for every product
```

Instead, record the product-specific value used by the experiment.

---

# 🗺️ TMC-2

## Terrain Mapping Camera-2

**Organization:** ISRO
**Type:** Official Instrument Documentation
**Reference Status:** Core
**Used for:** TMC-2 spatial sampling, swath, panchromatic imaging, stereo/terrain-mapping context.

ISRO describes TMC-2 as a panchromatic terrain-mapping camera with approximately 5 m spatial resolution and a 20 km swath from the nominal mission orbit; the payload supports lunar topographic and stereo/DEM-related science. ([ISRO][2])

[ISRO — Chandrayaan-2 Science / TMC-2](https://www.isro.gov.in/Chandrayaan2_science.html)

### Terminology

Use:

**TMC-2**

rather than casually shortening the Chandrayaan-2 instrument to **TMC**, which can be confused with the Chandrayaan-1 Terrain Mapping Camera.

---

# 🌈 IIRS

## Imaging Infrared Spectrometer

**Organization:** ISRO
**Type:** Official Instrument Documentation
**Reference Status:** Core
**Used for:** IIRS spectral modality, spectral coverage, spatial-resolution context, and product interpretation.

ISRO describes IIRS as a hyperspectral imaging instrument operating over approximately 0.8–5.0 µm. The mission handbook describes approximately 80 m spatial sampling and 256 contiguous spectral bands for the cited configuration. ([ISRO][4])

[ISRO — Chandrayaan-2 Payloads Data & Science Handbook](https://www.isro.gov.in/media_isro/pdf/science/hand_book_payloads_data_and_science.pdf)

[ISRO — IIRS Spectroscopic Studies of the Lunar Surface](https://www.isro.gov.in/Chandrayaan2begins.html)

### ChandraMap relevance

IIRS should not be treated as an ordinary grayscale camera input.

Research may need to construct a registration-friendly 2D representation using, for example:

- selected bands
- PCA-derived components
- spectral composites
- structural representations

No reference in this file establishes which representation is best for ChandraMap. That remains an experimental question.

---

# 🇺🇸 Lunar Reconnaissance Orbiter

## NASA Lunar Reconnaissance Orbiter Mission

**Organization:** NASA
**Type:** Official Mission Documentation
**Reference Status:** Core
**Used for:** LRO mission context, instrument suite, science/data resources, and lunar mapping context.

NASA's LRO documentation identifies LROC as one of the mission's scientific instruments and provides official mission/data resources. ([NASA Science][7])

[NASA Science — Lunar Reconnaissance Orbiter](https://science.nasa.gov/mission/lro/)

[NASA Science — LRO Data Products](https://science.nasa.gov/mission/lro/data-products/)

---

# 📷 LROC, NAC, and WAC

## Lunar Reconnaissance Orbiter Camera Instrument Overview

### Lunar Reconnaissance Orbiter Camera (LROC) Instrument Overview

**Authors:** M. S. Robinson et al.
**Venue:** _Space Science Reviews_
**Year:** 2010
**DOI:** `10.1007/s11214-010-9634-2`
**Type:** Primary Research / Instrument Paper
**Reference Status:** Core

The paper documents the LROC instrument system, including the two monochrome NACs and the multispectral WAC. Its nominal instrument description gives approximately 0.5 m/pixel for NAC observations and broader WAC sampling, but actual ChandraMap experiments should use product-specific scale metadata. ([USGS][8])

[USGS — LROC Instrument Overview Publication Record](https://www.usgs.gov/publications/lunar-reconnaissance-orbiter-camera-lroc-instrument-overview)

**Relevance to ChandraMap:**
Primary source for understanding the physical distinction between NAC and WAC and why their imagery should not be treated as interchangeable.

---

## LROC NAC Processing Guide

**Organization:** LROC Science Operations Center
**Type:** Official Processing Guide
**Reference Status:** Core
**Used for:** NAC product processing, calibration, ISIS conversion, map projection, matching scales/projections, and data-discovery resources.

The guide documents a current NAC processing workflow using ISIS and shows how calibrated data may be projected to common map geometry and pixel scale. ([LROC][9])

[LROC — NAC Processing Guide](https://www.lroc.asu.edu/data/support/downloads/LROC_NAC_Processing_Guide.pdf)

### ChandraMap relevance

This resource is particularly important when determining whether a selected NAC product is:

- raw/EDR
- calibrated
- map-projected
- geometrically suitable for local 2D registration

---

## LROC NAC Scale

NAC should **not** be represented by one universal product resolution.

The LROC instrument paper provides nominal instrument context, while observation geometry and processing determine product-specific scale. Use actual image/product metadata in experiments. ([USGS][8])

---

## LROC WAC

WAC is designed for wider-area multispectral/global lunar observations and serves a different role from the narrow-angle high-resolution cameras. The LROC instrument overview is the primary reference for that distinction. ([USGS][8])

**ChandraMap research uses may include:**

- broader lunar context
- regional/global mosaics
- retrieval/indexing research
- illumination context
- coarse reference imagery

WAC should not be treated as a drop-in replacement for NAC in fine-registration experiments.

---

# 🗃️ NASA Planetary Data System

## LROC PDS Collections

**Organization:** NASA Planetary Data System
**Type:** Official Data Archive
**Reference Status:** Core
**Used for:** LROC NAC/WAC EDR/CDR products, documentation, ancillary files, and reproducible data identification.

NASA PDS collections explicitly archive both NAC and WAC products and associated documentation. ([Planetary Data System][10])

[NASA PDS — LROC CDR Collection](https://pds.nasa.gov/ds-view/pds/viewCollection.jsp?identifier=urn:nasa:pds:lro-l-lroc-3-cdr:lrolrc_1066a_document&version=2.0)

### ChandraMap relevance

PDS identifiers and product metadata are preferable to informal filenames when preserving experiment provenance.

---

# 🪐 USGS ISIS and Planetary Processing

## ISIS Application Documentation

**Organization:** USGS Astrogeology Science Center
**Type:** Official Planetary-Processing Documentation
**Reference Status:** Core
**Used for:** Planetary registration, map projection, geometric processing, feature matching, and control-network workflows.

ISIS groups applications into areas including:

- map projection
- geometry
- control networks
- registration and pattern matching
- radiometric/photometric processing

and includes mission-specific planetary-processing support. ([USGS Isis][11])

[USGS ISIS — Application Documentation](https://isis.astrogeology.usgs.gov/dev/Application/index.html)

---

## ISIS `coreg`

**Organization:** USGS Astrogeology
**Type:** Official Software Documentation
**Reference Status:** Core
**Used for:** Image-to-image registration and sub-pixel planetary coregistration research.

ISIS `coreg` performs image-to-image coregistration using locally estimated translations and can generate control-network information. Its documentation also explicitly warns that flexible warping works best when control points are accurate and well distributed. ([USGS Isis][12])

[USGS ISIS — coreg Documentation](https://isis.astrogeology.usgs.gov/dev/Application/presentation/Tabbed/coreg/coreg.html)

**Relevance to ChandraMap:**

- sub-pixel registration research
- spatially distributed tie points
- local residual analysis
- planetary registration methodology

---

## ISIS `warp`

**Organization:** USGS Astrogeology
**Type:** Official Software Documentation
**Reference Status:** Recommended
**Used for:** Control-network-based geometric warping.

ISIS `warp` uses image-to-image control points from a control network to construct a geometric warp. The documentation makes clear that the result depends on the supplied control measurements. ([USGS Isis][13])

[USGS ISIS — warp Documentation](https://isis.astrogeology.usgs.gov/dev/Application/presentation/Tabbed/warp/warp.html)

> A flexible warp is not evidence that the underlying correspondences are correct.

---

## ISIS `findfeatures`

**Organization:** USGS Astrogeology
**Type:** Official Software Documentation
**Reference Status:** Recommended
**Used for:** Planetary feature-based image matching and control-network generation.

The ISIS `findfeatures` application uses a feature-based computer-vision workflow involving feature detection, descriptor extraction, and matching, built substantially on OpenCV feature-matching infrastructure. ([USGS Isis][14])

[USGS ISIS — findfeatures Documentation](https://isis.astrogeology.usgs.gov/dev/Application/presentation/Tabbed/findfeatures/findfeatures.html)

---

# 🌐 Map Projection and Planetary Geometry

Map projection is scientifically relevant whenever ChandraMap:

- compares ground scales
- interprets latitude/longitude
- converts image error to ground units
- aligns independently projected products
- uses local affine/projective approximations

The LROC NAC processing guide and ISIS documentation should be consulted before assuming that raw mission imagery can be treated as ordinary planar photographs. ([LROC][9])

> ChandraMap must not assume that every source or reference image is already map-projected.

---

# 🎯 Planetary Control Networks

Planetary control networks connect corresponding image measurements and support geometric consistency across imagery.

Relevant ChandraMap concepts include:

- control/tie points
- spatial distribution
- image-to-image constraints
- refinement
- planetary warping
- geometric validation

ISIS `coreg` and `warp` provide practical official references for image-to-image control-point workflows. ([USGS Isis][12])

Control points used to fit a transform should still be distinguished from independent check points used to evaluate it.

---

# 🔍 Classical Correspondence and Registration

## SIFT

### Distinctive Image Features from Scale-Invariant Keypoints

**Author:** David G. Lowe
**Venue:** _International Journal of Computer Vision_
**Year:** 2004
**Volume:** 60(2), 91–110
**DOI:** `10.1023/B:VISI.0000029664.99615.94`
**Type:** Primary Research Paper
**Reference Status:** Core / Baseline

Lowe's paper defines the Scale-Invariant Feature Transform (SIFT), which detects and describes local image features with scale and rotation robustness. ([DBLP][15])

**Relevance to ChandraMap:**
SIFT provides an explainable classical local-feature baseline against which additional preprocessing and modern correspondence methods can be compared.

---

## RootSIFT

### Three Things Everyone Should Know to Improve Object Retrieval

**Authors:** Relja Arandjelović, Andrew Zisserman
**Venue:** IEEE Conference on Computer Vision and Pattern Recognition (CVPR)
**Year:** 2012
**DOI:** `10.1109/CVPR.2012.6248018`
**Type:** Primary Research Paper
**Reference Status:** Experimental / Candidate

The paper introduces the RootSIFT descriptor transformation as part of an image-retrieval study. ([IEEE Xplore][16])

**Relevance to ChandraMap:**
RootSIFT is a low-complexity classical descriptor variant worth evaluating against the base SIFT representation.

Its inclusion here does not imply that it is currently implemented.

---

# Robust Geometric Estimation

## RANSAC

### Random Sample Consensus: A Paradigm for Model Fitting with Applications to Image Analysis and Automated Cartography

**Authors:** Martin A. Fischler, Robert C. Bolles
**Venue:** _Communications of the ACM_
**Year:** 1981
**Volume:** 24(6), 381–395
**DOI:** `10.1145/358669.358692`
**Type:** Primary Research Paper
**Reference Status:** Core

The paper introduces RANSAC as a robust model-fitting method designed to tolerate data containing gross outliers. ([CiNii Research][17])

**Relevance to ChandraMap:**
RANSAC supports geometric verification of candidate correspondences before those matches are treated as model-consistent inliers.

RANSAC does not establish ground truth and does not guarantee that the chosen geometric model is physically adequate.

---

# OpenCV

## Feature Matching with FLANN

**Organization:** OpenCV
**Type:** Official Software Documentation
**Reference Status:** Core / Baseline Support
**Used for:** Descriptor matching, nearest-neighbor ratio filtering, and classical correspondence concepts.

The OpenCV tutorial discusses matching floating-point descriptors such as SIFT, the nearest-neighbor ratio test, cross-checking, and geometric verification. ([OpenCV Documentation][18])

[OpenCV — Feature Matching with FLANN](https://docs.opencv.org/4.x/d5/d6f/tutorial_feature_flann_matcher.html)

---

## Feature Matching and Homography

**Organization:** OpenCV
**Type:** Official Software Documentation
**Reference Status:** Core / Baseline Support
**Used for:** Homography estimation from matched features and perspective transformation.

OpenCV's official tutorial demonstrates matched-feature geometry with `findHomography` and RANSAC-based estimation. ([OpenCV Documentation][19])

[OpenCV — Features2D + Homography](https://docs.opencv.org/4.x/d7/dff/tutorial_feature_homography.html)

### ChandraMap caution

A homography is useful as a local image model. It should not be interpreted as a universal physical model of lunar terrain or raw orbital sensor geometry.

---

# 🎯 Sub-Pixel Registration

## USGS ISIS Coregistration

ISIS `coreg` is the primary planetary-processing reference in this index for sub-pixel image-to-image registration. Its documentation distinguishes simple image-wide translation from spatially varying registration and notes the importance of well-distributed accurate control points. ([USGS Isis][12])

[USGS ISIS — coreg](https://isis.astrogeology.usgs.gov/dev/Application/presentation/Tabbed/coreg/coreg.html)

### Research interpretation

A sub-pixel estimate should remain tied to:

- its image coordinate space
- its source/reference definition
- its evaluation population
- its product-specific GSD if converted to ground units

Sub-pixel registration does not reconstruct spatial information absent from a coarse source sensor.

---

# 🧠 Learned Local Matching

The methods below are **research candidates unless repository implementation evidence says otherwise**.

Their published performance on terrestrial computer-vision datasets must not be treated as evidence of lunar robustness.

---

## ALIKED

### ALIKED: A Lighter Keypoint and Descriptor Extraction Network via Deformable Transformation

**Authors:** Xiaoming Zhao, Xingming Wu, Weihai Chen, Peter C. Y. Chen, Qingsong Xu, Zhengguo Li
**Venue:** _IEEE Transactions on Instrumentation and Measurement_
**Year:** 2023
**DOI:** `10.1109/TIM.2023.3271000`
**Type:** Primary Research Paper / Official Repository
**Reference Status:** Experimental

ALIKED is a learned local keypoint and descriptor extraction network. ([GitHub][20])

[ALIKED — Official Author Repository](https://github.com/Shiaoming/ALIKED)

**Relevance to ChandraMap:**
Candidate learned local-feature extractor for controlled comparison against classical features.

---

## LightGlue

### LightGlue: Local Feature Matching at Light Speed

**Authors:** Philipp Lindenberger, Paul-Edouard Sarlin, Marc Pollefeys
**Venue:** IEEE/CVF International Conference on Computer Vision (ICCV)
**Year:** 2023
**Pages:** 17627–17638
**DOI:** `10.1109/ICCV51070.2023.01616`
**Type:** Primary Research Paper / Official Repository
**Reference Status:** Experimental

LightGlue is a learned matcher for **sparse local features**. It consumes local keypoints/descriptors rather than serving as the feature detector itself. Its official implementation supplies pretrained support for local features including ALIKED and SIFT. ([Association for Computing Machinery][21])

[CVF — LightGlue Paper](https://openaccess.thecvf.com/content/ICCV2023/html/Lindenberger_LightGlue_Local_Feature_Matching_at_Light_Speed_ICCV_2023_paper.html)

[LightGlue — Official Repository](https://github.com/cvg/LightGlue)

### Correct conceptual separation

```text
ALIKED
→ local keypoints + descriptors
→ LightGlue
→ matched sparse features
```

Do not describe LightGlue itself as an ALIKED-like keypoint detector.

---

## LoFTR

### LoFTR: Detector-Free Local Feature Matching with Transformers

**Authors:** Jiaming Sun, Zehong Shen, Yuang Wang, Hujun Bao, Xiaowei Zhou
**Venue:** IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR)
**Year:** 2021
**Pages:** 8922–8931
**DOI:** `10.1109/CVPR46437.2021.00881`
**Type:** Primary Research Paper / Official Repository
**Reference Status:** Experimental

LoFTR is a detector-free local feature-matching method that establishes coarse correspondences before refining them. ([Association for Computing Machinery][22])

[CVF — LoFTR Paper](https://openaccess.thecvf.com/content/CVPR2021/html/Sun_LoFTR_Detector-Free_Local_Feature_Matching_With_Transformers_CVPR_2021_paper.html)

[LoFTR — Official Repository](https://github.com/zju3dv/LoFTR)

**Relevance to ChandraMap:**
Potential candidate when repeatable sparse keypoint detection is weak.

Its terrestrial benchmark performance should not be interpreted as proof of lunar cross-sensor robustness.

---

# 🌐 Multimodal Remote-Sensing Registration

## RIFT

### RIFT: Multi-modal Image Matching Based on Radiation-variation Insensitive Feature Transform

**Authors:** Jiayuan Li, Qingwu Hu, Mingyao Ai
**Venue:** _IEEE Transactions on Image Processing_
**Year:** 2019
**DOI:** `10.1109/TIP.2019.2959244`
**Type:** Primary Research Paper
**Reference Status:** Experimental

RIFT is designed for multimodal image matching using structural information intended to reduce sensitivity to nonlinear radiometric differences. ([PubMed][23])

[Primary Publication Record — RIFT](https://pubmed.ncbi.nlm.nih.gov/31869789/)

**Relevance to ChandraMap:**

- multimodal correspondence
- structural matching
- radiometric differences
- remote-sensing registration

Listing RIFT here does not mean ChandraMap currently implements it.

---

## CFOG

### Fast and Robust Matching for Multimodal Remote Sensing Image Registration

**Authors:** Yuanxin Ye, Lorenzo Bruzzone, Jie Shan, Francesca Bovolo, Qing Zhu
**Venue:** _IEEE Transactions on Geoscience and Remote Sensing_
**Year:** 2019
**Volume:** 57(11), 9059–9070
**DOI:** `10.1109/TGRS.2019.2924684`
**Type:** Primary Research Paper
**Reference Status:** Experimental

The paper introduces **Channel Features of Oriented Gradients (CFOG)** within a multimodal remote-sensing matching framework using structural gradient representations. ([DOI][24])

[DOI — Fast and Robust Matching for Multimodal Remote Sensing Image Registration](https://doi.org/10.1109/TGRS.2019.2924684)

[CFOG — Author Repository](https://github.com/yeyuanxin110/CFOG)

**Relevance to ChandraMap:**
Potential research direction for pairs where raw intensity similarity is weak across sensor modalities.

---

## Multimodal Registration Research Boundary

RIFT and CFOG are useful research references because ChandraMap contains sensor pairs with major radiometric and modality differences.

They should not be assumed to:

- work unchanged on lunar data
- outperform SIFT
- solve IIRS registration
- remove the need for geometric verification

Their value must be measured under controlled lunar experiments.

---

# 🔎 Global Retrieval and Indexing

## FAISS

**Organization:** Meta / Facebook Research
**Type:** Official Software Repository
**Reference Status:** Experimental / Retrieval Infrastructure
**Used for:** Dense-vector similarity search and indexing.

FAISS is a library for efficient similarity search and clustering of dense vectors. It assumes that the entities being searched have already been represented as vectors. ([GitHub][25])

[FAISS — Official Repository](https://github.com/facebookresearch/faiss)

### Correct ChandraMap role

```text
Reference Tile
→ Global Descriptor
→ FAISS Index

Query Image
→ Compatible Global Descriptor
→ FAISS Search
→ Top-K Candidate Regions
```

FAISS does **not** itself:

- detect lunar features
- create image embeddings automatically
- compute local correspondences
- estimate transforms
- perform registration

---

## Global Descriptor Selection

ChandraMap has not established one universal global image descriptor in this reference index.

If a descriptor is formally selected later:

1. add its primary paper
2. add its official implementation
3. document its input representation
4. document how reference/query descriptors are produced
5. benchmark retrieval separately from registration

Do not cite FAISS as though FAISS defines the visual descriptor.

---

# ☀️ Illumination and Photometric Processing

Lunar illumination research should distinguish photometric/radiometric normalization from geometric shadow changes.

Relevant current authoritative resources include:

- Chandrayaan-2 mission/product illumination metadata where available
- LROC product metadata
- USGS ISIS radiometric/photometric processing documentation

ISIS exposes dedicated photometric and illumination-related planetary-processing categories and applications, reinforcing that planetary brightness/geometry requires domain-aware treatment rather than generic image normalization alone. ([USGS Isis][11])

[USGS ISIS — Application Documentation](https://isis.astrogeology.usgs.gov/dev/Application/index.html)

ChandraMap should add more specialized lunar photometry papers only when an experiment directly depends on a defined photometric model.

---

# 📊 Evaluation and Benchmarking References

ChandraMap evaluation is primarily governed by its own documented benchmark/evaluation contract rather than by a single external paper.

External references support individual concepts:

| Evaluation Concept                    | Primary Supporting Resource                   |
| ------------------------------------- | --------------------------------------------- |
| Robust model fitting                  | Fischler & Bolles RANSAC                      |
| SIFT matching/filtering               | Lowe; OpenCV                                  |
| Image-to-image sub-pixel registration | USGS ISIS `coreg`                             |
| Distributed control support / warping | USGS ISIS `coreg` / `warp`                    |
| Retrieval ranking                     | Retrieval method + benchmark-defined Recall@K |
| Product geometry                      | Mission metadata / planetary processing docs  |

### Important distinction

A transformation fitted from one population should not automatically be claimed to have independent accuracy based only on its fit residual.

ChandraMap's definitions for:

- fit/control points
- held-out check points
- RMSE
- spatial coverage
- success/failure
- benchmark aggregation

belong in project evaluation documentation.

---

# Spatial Coverage References

ChandraMap may use metrics such as:

- grid coverage
- convex-hull coverage

to quantify whether verified correspondences are distributed across the valid overlap.

If these are defined specifically by ChandraMap rather than adopted from an external method, they should be described as **project-defined evaluation metrics** and documented in the project's evaluation documentation rather than falsely attributed to an unrelated paper.

---

# 🔁 Reproducibility and Citation

## Mission Data

When an archive provides citation or acknowledgement instructions, follow that archive's current guidance.

For example, the JAXA DARTS SELENE archive provides explicit acknowledgement guidance for use of Kaguya/SELENE data. ([DARTS at ISAS/JAXA][26])

Do not invent a dataset citation.

---

## Scientific Software

Prefer a software project's own:

- citation file
- citation documentation
- paper
- DOI
- official repository

when citing software.

Do not guess BibTeX records.

---

## ChandraMap

Use the repository's citation metadata when citing ChandraMap:

- [`../../CITATION.cff`](../../CITATION.cff)

Do not invent:

- a release DOI
- a Zenodo DOI
- an institutional affiliation
- a publication citation

unless one actually exists.

---

# 🌍 Optional / Future Cross-Mission References

## Kaguya / SELENE Terrain Camera

**Organization:** JAXA / ISAS
**Type:** Official Mission Data Archive
**Reference Status:** Optional / Future Research
**Used for:** Potential cross-mission lunar correspondence, terrain data, orthorectified products, and validation research.

The DARTS Kaguya archive contains Terrain Camera datasets, including observation imagery and derived DTM/orthographic products. ([DARTS at ISAS/JAXA][27])

[JAXA DARTS — Kaguya PDS Archive](https://darts.isas.jaxa.jp/missions/pds/pds_kaguya_en.html)

---

## SELENE Terrain Camera DTM / Ortho Products

**Organization:** ISAS/JAXA DARTS
**Type:** Official Product Archive
**Reference Status:** Optional / Future Research

DARTS provides Terrain Camera DTM and orthographic products derived from stereo observations and documents their map-projection context. ([DARTS at ISAS/JAXA][26])

[JAXA DARTS — SELENE DTM and TC Ortho Data](https://darts.isas.jaxa.jp/en/datasets/darts%3Asln-l-tc-4-dtm-ortho-v3.0)

Kaguya/SELENE should remain an optional cross-mission research source unless it is explicitly incorporated into a formal ChandraMap benchmark.

---

# 💻 Research Software References

Only major software directly connected to the scientific methodology belongs here.

| Software      | Research Role                                                              |
| ------------- | -------------------------------------------------------------------------- |
| **OpenCV**    | Classical local features, descriptor matching, RANSAC/homography utilities |
| **USGS ISIS** | Planetary image processing, projection, control networks, registration     |
| **FAISS**     | Vector indexing and similarity retrieval                                   |
| **LightGlue** | Learned sparse local-feature matching, if experimentally used              |
| **LoFTR**     | Detector-free correspondence research, if experimentally used              |
| **ALIKED**    | Learned local feature extraction, if experimentally used                   |

This section should not duplicate the project's complete dependency manifest.

---

# Reference-to-Research Mapping

| ChandraMap Research Area           | Primary References                         |
| ---------------------------------- | ------------------------------------------ |
| OHRC properties                    | ISRO payload/science documents             |
| TMC-2 properties                   | ISRO payload/science documents             |
| IIRS modality and spectral context | ISRO handbook / IIRS science documentation |
| Chandrayaan-2 data access          | PRADAN / ISSDC                             |
| NAC/WAC instrument context         | Robinson et al. LROC instrument overview   |
| NAC processing                     | LROC NAC Processing Guide                  |
| LRO data access                    | NASA PDS / LROC                            |
| Planetary processing               | USGS ISIS                                  |
| Classical local features           | Lowe SIFT                                  |
| Descriptor filtering               | Lowe / OpenCV                              |
| Robust geometry                    | Fischler & Bolles RANSAC                   |
| Homography estimation              | OpenCV                                     |
| Planetary sub-pixel registration   | USGS ISIS `coreg`                          |
| Sparse learned matching            | LightGlue                                  |
| Learned feature extraction         | ALIKED                                     |
| Detector-free matching             | LoFTR                                      |
| Multimodal structural matching     | RIFT / CFOG                                |
| Vector retrieval                   | FAISS                                      |
| Future cross-mission validation    | JAXA DARTS / SELENE TC                     |

---

# Product-Specific Values

Instrument-level numbers in this document provide research context.

They are not substitutes for product metadata.

For an actual experiment, record the specific product's:

- product identifier
- instrument
- processing level
- dimensions
- GSD/pixel scale
- projection
- coordinate reference
- acquisition geometry
- illumination metadata
- spectral/band configuration where relevant

> **Product metadata should override a generic instrument summary whenever the two differ in experiment-specific details.**

This is especially important for:

- OHRC GSD
- NAC pixel scale
- WAC scale
- off-nadir imagery
- projected products
- IIRS band/product characteristics

---

# Handling Conflicting Authoritative Sources

When reputable official sources report different values:

1. do not silently select whichever value is more convenient
2. determine whether they refer to different:
   - product levels
   - acquisition geometry
   - nominal vs operational specifications
   - mission phases
   - processing conventions

3. preserve the distinction in project documentation
4. use actual product metadata for the experiment

The OHRC 0.25 m / 0.32 m documentation difference is an example of why ChandraMap should not encode one instrument-level number as universal product truth. ([ISRO][6])

---

# 📚 ChandraMap Internal Documentation

Research references should be interpreted together with ChandraMap's own methodology and scope documentation.

## Research

- [Research Overview](README.md) — entry point for ChandraMap research
- [Baseline](baseline.md) — classical baseline methodology
- [Research Questions](research-questions.md) — questions currently driving experimentation
- [Research Assumptions](assumptions.md) — assumptions and validation dependencies
- [Experiment Methodology](experiment-methodology.md) — controlled experimental methodology

## Project

- [Project Goals](../project/goals.md)
- [Project Non-Goals](../project/non-goals.md)
- [V1 Scope](../project/v1-scope.md)
- [Project Terminology](../project/terminology.md)
- [Project Limitations](../project/limitations.md)

## Architecture

- [System Overview](../architecture/system-overview.md)
- [V1 Pipeline](../architecture/v1-pipeline.md)
- [Core Engine Architecture](../architecture/core-engine-architecture.md)
- [Data Flow](../architecture/data-flow.md)
- [Output Flow](../architecture/output-flow.md)

## Sensors

- [Sensors Overview](../sensors/overview.md)

## Development and Benchmarking

- [Benchmarking Guide](../development/benchmarking.md)
- [Naming Conventions](../development/naming-conventions.md)
- [Coding Standards](../development/coding-standards.md)
- [Documentation Guide](../development/documentation-guide.md)

---

# 🧾 Citation Guidance

## Mission and Instrument Facts

Cite official mission or instrument documentation.

Prefer:

```text
official instrument documentation
```

over:

```text
secondary article summarizing the instrument
```

---

## Mission Products

For experiment-specific values, preserve the actual product identifier and metadata.

Do not cite a generic mission page as evidence of a product-specific GSD when the product metadata provides a more precise value.

---

## Research Papers

When citing an algorithm:

1. cite the original paper
2. cite the official implementation separately if code behavior matters
3. do not imply the method is implemented in ChandraMap merely because it is referenced here

---

## Software

When software provides a preferred citation, follow its official instructions.

Use official repositories or documentation rather than third-party copies.

---

# ➕ Adding a Reference

Before adding a reference, confirm that it answers at least one of these questions:

- Does it define a sensor?
- Does it define a mission product?
- Does it provide required data?
- Does it define a method ChandraMap uses or evaluates?
- Does it support planetary processing?
- Does it support evaluation methodology?
- Does it directly inform a current or planned ChandraMap research question?

If none apply, it probably does not belong in this curated index.

### Contribution procedure

1. Verify the resource exists.
2. Verify the title.
3. Verify the organization or authors.
4. Verify publication year if included.
5. Verify DOI before writing it.
6. Prefer the primary source.
7. Prefer the official implementation.
8. Add a concise relevance annotation.
9. Place the resource in the correct category.
10. Check for duplicates.
11. Avoid implying implementation status.
12. Avoid attaching claims that the source does not support.

---

# Reference Review Checklist

Before merging a reference change:

- [ ] Reference is relevant to ChandraMap
- [ ] Primary/official source was preferred
- [ ] Title was verified
- [ ] Organization or authors were verified
- [ ] Year was verified where included
- [ ] DOI was verified where included
- [ ] Resource remains accessible
- [ ] Official repository was used instead of a fork
- [ ] No stronger authoritative source was ignored
- [ ] Annotation accurately describes the resource
- [ ] No unsupported scientific claim is attached
- [ ] Reference does not imply unsupported implementation status
- [ ] Reference does not imply benchmark superiority
- [ ] Product-specific values are not replaced by instrument summaries
- [ ] Duplicate references were avoided
- [ ] Internal links still point to valid project documentation

---

# Reference Maintenance

This file should be reviewed when:

- mission portals move
- official documentation is superseded
- data archives change
- a new scientific method enters a formal experiment
- ChandraMap selects a global descriptor
- later scientific versions introduce new methods
- new benchmark methodology requires a primary source
- optional mission data becomes part of a formal benchmark

Historical primary papers should not be removed merely because they are old. Their age is appropriate when they define foundational methods such as SIFT or RANSAC.

Software-documentation links, however, should normally point to current official documentation.

---

# Summary

ChandraMap references should remain **authoritative, primary where possible, technically relevant, and traceable to the scientific claims they support**.

The most important reference discipline is:

> **Use official mission documentation for sensors and products, primary papers for algorithms, official documentation for software, and ChandraMap's own reproducible evidence for claims about ChandraMap performance.**

A reference establishes what a mission, instrument, algorithm, or software package is. It does not by itself establish that the corresponding method is implemented, appropriate, or superior for ChandraMap.

<!-- Source request: :contentReference[oaicite:72]{index=72} -->

[1]: https://www.isro.gov.in/chandrayaan2-payloads.html "https://www.isro.gov.in/chandrayaan2-payloads.html"
[2]: https://www.isro.gov.in/Chandrayaan2_science.html "https://www.isro.gov.in/Chandrayaan2_science.html"
[3]: https://www.isro.gov.in/ScienceandDataProduct.html "https://www.isro.gov.in/ScienceandDataProduct.html"
[4]: https://www.isro.gov.in/media_isro/pdf/Publications/Sciencedata/hand_book_-_payloads_data_and_science.pdf "https://www.isro.gov.in/media_isro/pdf/Publications/Sciencedata/hand_book_-_payloads_data_and_science.pdf"
[5]: https://pradan.issdc.gov.in/ch2/ "https://pradan.issdc.gov.in/ch2/"
[6]: https://www.isro.gov.in/media_isro/pdf/science/science_results_from_ch.pdf "https://www.isro.gov.in/media_isro/pdf/science/science_results_from_ch.pdf"
[7]: https://science.nasa.gov/mission/lro/ "https://science.nasa.gov/mission/lro/"
[8]: https://www.usgs.gov/publications/lunar-reconnaissance-orbiter-camera-lroc-instrument-overview "https://www.usgs.gov/publications/lunar-reconnaissance-orbiter-camera-lroc-instrument-overview"
[9]: https://www.lroc.asu.edu/data/support/downloads/LROC_NAC_Processing_Guide.pdf "https://www.lroc.asu.edu/data/support/downloads/LROC_NAC_Processing_Guide.pdf"
[10]: https://pds.nasa.gov/ds-view/pds/viewCollection.jsp?identifier=urn%3Anasa%3Apds%3Alro-l-lroc-3-cdr%3Alrolrc_1066a_document&version=2.0 "https://pds.nasa.gov/ds-view/pds/viewCollection.jsp?identifier=urn%3Anasa%3Apds%3Alro-l-lroc-3-cdr%3Alrolrc_1066a_document&version=2.0"
[11]: https://isis.astrogeology.usgs.gov/dev/Application/index.html "https://isis.astrogeology.usgs.gov/dev/Application/index.html"
[12]: https://isis.astrogeology.usgs.gov/dev/Application/presentation/Tabbed/coreg/coreg.html "https://isis.astrogeology.usgs.gov/dev/Application/presentation/Tabbed/coreg/coreg.html"
[13]: https://isis.astrogeology.usgs.gov/8.3.0/Application/presentation/PrinterFriendly/warp/warp.html "https://isis.astrogeology.usgs.gov/8.3.0/Application/presentation/PrinterFriendly/warp/warp.html"
[14]: https://isis.astrogeology.usgs.gov/dev/Application/presentation/Tabbed/findfeatures/findfeatures.html "https://isis.astrogeology.usgs.gov/dev/Application/presentation/Tabbed/findfeatures/findfeatures.html"
[15]: https://dblp.org/rec/journals/ijcv/Lowe04.html "https://dblp.org/rec/journals/ijcv/Lowe04.html"
[16]: https://ieeexplore.ieee.org/document/6248018/ "https://ieeexplore.ieee.org/document/6248018/"
[17]: https://cir.nii.ac.jp/crid/1360292619284965760 "https://cir.nii.ac.jp/crid/1360292619284965760"
[18]: https://docs.opencv.org/4.x/d5/d6f/tutorial_feature_flann_matcher.html "https://docs.opencv.org/4.x/d5/d6f/tutorial_feature_flann_matcher.html"
[19]: https://docs.opencv.org/4.x/d7/dff/tutorial_feature_homography.html "https://docs.opencv.org/4.x/d7/dff/tutorial_feature_homography.html"
[20]: https://github.com/Shiaoming/ALIKED "https://github.com/Shiaoming/ALIKED"
[21]: https://openaccess.thecvf.com/content/ICCV2023/html/Lindenberger_LightGlue_Local_Feature_Matching_at_Light_Speed_ICCV_2023_paper.html "https://openaccess.thecvf.com/content/ICCV2023/html/Lindenberger_LightGlue_Local_Feature_Matching_at_Light_Speed_ICCV_2023_paper.html"
[22]: https://openaccess.thecvf.com/content/CVPR2021/html/Sun_LoFTR_Detector-Free_Local_Feature_Matching_With_Transformers_CVPR_2021_paper.html "https://openaccess.thecvf.com/content/CVPR2021/html/Sun_LoFTR_Detector-Free_Local_Feature_Matching_With_Transformers_CVPR_2021_paper.html"
[23]: https://pubmed.ncbi.nlm.nih.gov/31869789/ "https://pubmed.ncbi.nlm.nih.gov/31869789/"
[24]: https://doi.org/10.1109/TGRS.2019.2924684 "https://doi.org/10.1109/TGRS.2019.2924684"
[25]: https://github.com/facebookresearch/faiss "https://github.com/facebookresearch/faiss"
[26]: https://darts.isas.jaxa.jp/en/datasets/darts%3Asln-l-tc-4-dtm-ortho-v3.0 "https://darts.isas.jaxa.jp/en/datasets/darts%3Asln-l-tc-4-dtm-ortho-v3.0"
[27]: https://darts.isas.jaxa.jp/missions/pds/pds_kaguya_en.html "https://darts.isas.jaxa.jp/missions/pds/pds_kaguya_en.html"
