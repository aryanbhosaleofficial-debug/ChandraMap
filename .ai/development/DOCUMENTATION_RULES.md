# ChandraMap Documentation Rules

This document defines how **ChandraMap** documentation must be written, verified, organized, and maintained.

Its purpose is to help human contributors and AI coding agents answer:

> **How should ChandraMap documentation be written so that it remains accurate, professional, scientifically honest, navigable, reproducible, and synchronized with the repository?**

The primary rule is:

> **Document reality, not intention.**

If something is implemented, describe it as implemented.

If something is planned, describe it as planned.

If something is experimental, describe it as experimental.

If something is proposed, describe it as proposed.

If something is only documented, do not imply that it exists in code.

---

## 1. Purpose

`DOCUMENTATION_RULES.md` defines standards for:

- root repository documentation
- `.ai/` documentation
- architecture documentation
- development documentation
- benchmark documentation
- research documentation
- dataset documentation
- backend/API documentation
- frontend/user documentation
- READMEs
- code examples
- commands
- configuration examples
- repository trees
- diagrams
- tables
- links
- citations
- screenshots
- scientific claims
- benchmark results
- implementation-status wording
- roadmap wording
- changelog entries
- release documentation
- generated documentation
- documentation review
- documentation maintenance

This file defines documentation policy.

It is not:

- `README.md`
- `CONTRIBUTING.md`
- `AGENTS.md`
- `ENGINEERING_RULES.md`
- `CODING_RULES.md`
- `TESTING_RULES.md`
- architecture documentation
- benchmark methodology
- API reference
- a research paper
- a Markdown tutorial

---

## 2. Documentation Priorities

When documentation goals conflict, use approximately this priority:

1. technical correctness
2. scientific correctness
3. repository consistency
4. implementation-status accuracy
5. reproducibility
6. clarity
7. navigability
8. maintainability
9. conciseness
10. visual polish

A polished document containing incorrect technical or scientific claims is not professional documentation.

---

## 3. Source-of-Truth Rule

Before documenting repository-specific behavior, inspect the current repository whenever possible.

Relevant evidence may include:

- source code
- tests
- configuration
- package manifests
- command definitions
- CLI code
- API contracts
- dataset manifests
- benchmark manifests
- CI workflows
- scripts
- existing documentation
- release metadata

Do not invent information merely because it would be useful to document.

For example, do not document:

```text
src/chandramap/retrieval/faiss.py
```

unless that path actually exists.

Do not document:

```bash
pytest tests/
```

unless the repository's tooling confirms that command is valid.

---

## 4. Evidence Hierarchy

When sources disagree, resolve the conflict deliberately.

A useful priority is:

| Priority | Evidence                                    |
| -------- | ------------------------------------------- |
| 1        | Explicit current project/user requirement   |
| 2        | Current repository implementation           |
| 3        | Current configuration, contracts, and tests |
| 4        | Current authoritative project documentation |
| 5        | Roadmap, target architecture, or proposal   |
| 6        | Historical or obsolete documentation        |

Do not automatically treat an old README as more authoritative than current code.

If ambiguity cannot be resolved, document the uncertainty instead of inventing an answer.

---

## 5. Document Reality, Not Intention

Documentation must distinguish what exists from what is desired.

Bad:

> ChandraMap uses FAISS to search the whole Moon.

when global retrieval is only planned.

Better:

> A future retrieval configuration may use a vector-search index such as FAISS.

Bad:

> ChandraMap performs DEM-aware terrain correction.

when that capability is only under investigation.

Better:

> DEM-aware geometry is a proposed research direction for later benchmark configurations.

---

## 6. Status Language

Use status terms consistently.

| Status           | Meaning                                                       |
| ---------------- | ------------------------------------------------------------- |
| **Implemented**  | Present in current code                                       |
| **Tested**       | Actually validated through executed tests or checks           |
| **Documented**   | Explained in documentation; does not imply implementation     |
| **Experimental** | Prototype/research behavior with limited stability guarantees |
| **Planned**      | Intended future work                                          |
| **Proposed**     | Idea under consideration                                      |
| **Supported**    | Intentionally maintained/currently accepted for use           |
| **Deprecated**   | Still present but intended for replacement or removal         |
| **Removed**      | No longer present                                             |

Do not use these terms interchangeably.

In particular:

> **Documented ≠ Implemented**

and:

> **Tests exist ≠ Tests passed**

---

## 7. Current vs Target Architecture

Architecture documentation must clearly distinguish:

### Current

Verified current implementation.

### Target

Approved intended architecture that may not yet exist.

### Experimental

Prototype or research functionality.

### Planned

Future functionality.

A target architecture diagram must not be titled:

> Current ChandraMap Architecture

unless every relevant part has been verified as current.

Where a document mixes current and target components, label those distinctions close to the relevant sections or diagrams.

---

## 8. Documentation Types and Audiences

Different documents serve different readers.

| Documentation Type         | Primary Audience                       | Primary Purpose                                                         |
| -------------------------- | -------------------------------------- | ----------------------------------------------------------------------- |
| Root documentation         | New visitors, contributors, evaluators | Project orientation and repository governance                           |
| `.ai/` documentation       | AI coding agents and maintainers       | Structured repository context and engineering rules                     |
| Architecture documentation | Engineers and researchers              | System design and responsibility boundaries                             |
| Development documentation  | Contributors                           | Coding, testing, configuration, dependency, and documentation standards |
| Benchmark documentation    | Research/benchmark engineers           | Controlled comparison methodology                                       |
| Research documentation     | Researchers                            | Experiments, hypotheses, methods, findings                              |
| Dataset documentation      | Data/scientific contributors           | Product sources, metadata, provenance, acquisition                      |
| User documentation         | Users                                  | Installation and usage                                                  |
| API documentation          | API consumers                          | Public interfaces and contracts                                         |

Do not force every audience into one document.

---

## 9. Audience-First Writing

Before writing a document, identify:

- who needs it
- what question it answers
- what level of detail they require
- what information belongs elsewhere

Examples:

`README.md`
→ new visitor or contributor

`AGENTS.md`
→ AI coding agent

`SYSTEM_OVERVIEW.md`
→ engineer understanding architecture

`DATASETS.md`
→ contributor working with scientific products

`TESTING_RULES.md`
→ contributor validating software/scientific behavior

A document should not become an encyclopedia simply because related information exists.

---

## 10. Document Purpose Statements

Important documentation should make its purpose clear near the beginning.

A reader should quickly understand:

- what the document defines
- what it does not define
- which documents provide deeper details

Avoid repeating a long introduction to ChandraMap at the beginning of every file.

---

## 11. Single Source of Truth

Prefer one authoritative location for each major subject.

Conceptually:

| Document                   | Primary Responsibility                     |
| -------------------------- | ------------------------------------------ |
| `PROJECT_CONTEXT.md`       | Project identity, purpose, scope           |
| `DOMAIN_CONTEXT.md`        | Scientific and physical domain constraints |
| `TERMINOLOGY.md`           | Canonical terminology                      |
| `DATASETS.md`              | Dataset/product context and provenance     |
| `V1_SCOPE.md`              | Canonical Benchmark V1 boundary            |
| `SYSTEM_OVERVIEW.md`       | System architecture                        |
| `PIPELINE.md`              | Processing order                           |
| `MODULE_MAP.md`            | Repository ownership                       |
| `DATA_FLOW.md`             | Information movement and semantics         |
| `CODING_RULES.md`          | Source-code standards                      |
| `TESTING_RULES.md`         | Testing standards                          |
| `DOCUMENTATION_RULES.md`   | Documentation standards                    |
| `METRICS.md`, when present | Exact metric definitions                   |

Prefer:

```text
one authoritative explanation
        +
cross-reference
```

over copying the same detailed explanation into several files.

---

## 12. Avoid Documentation Duplication

Duplication creates drift.

For example:

`DOMAIN_CONTEXT.md` may define why GSD matters scientifically.

`PIPELINE.md` should explain where GSD affects processing without reproducing the entire scientific explanation.

`TERMINOLOGY.md` should define `GSD` concisely rather than duplicating the domain discussion.

When detailed information already has an owner:

> Link to or reference the authoritative document.

Do not maintain several independent versions of the same rule.

---

## 13. Root Documentation vs `.ai/`

Keep these responsibilities distinct.

### Root and `docs/`

Primarily human-facing documentation.

It should remain understandable without requiring a reader to consume the complete `.ai/` hierarchy.

### `.ai/`

Primarily structured engineering context for AI agents and maintainers.

It may be more explicit about:

- repository ownership
- scientific invariants
- context-loading rules
- source-of-truth rules
- safe modification behavior

Do not copy root documentation word-for-word into `.ai/`.

---

## 14. Root Documentation Responsibilities

### `README.md`

The primary entry point for a new visitor.

It may summarize:

- what ChandraMap is
- the problem
- core capabilities
- current project status
- high-level architecture
- setup/usage where verified
- benchmark structure
- contribution links
- license
- citation information

Do not turn `README.md` into the complete architecture manual or research report.

---

### `AGENTS.md`

Provides repository-level operating guidance for AI coding agents.

It should route agents toward specialized `.ai/` documentation rather than duplicating all of it.

---

### `ROADMAP.md`

Describes intended future development.

Use wording such as:

- Planned
- Proposed
- Target
- Under investigation

Do not present future roadmap items as current functionality.

---

### `CHANGELOG.md`

Documents notable completed changes.

Do not use it as:

- a roadmap
- raw commit history
- benchmark-results database

Do not invent release numbers, release dates, or completed changes.

---

### `SECURITY.md`

Owns vulnerability-reporting and security-policy information.

Do not duplicate security reporting instructions throughout unrelated documentation.

---

### `CONTRIBUTING.md`

Owns contributor workflow.

`DOCUMENTATION_RULES.md` defines documentation quality standards, not the complete contribution process.

---

### `CITATION.cff`

Defines repository citation metadata where used.

Do not manually duplicate citation metadata across many documents if one canonical citation file exists.

---

# 15. Scientific Documentation Rules

ChandraMap documentation must distinguish:

### Fact

Supported by scientific knowledge, authoritative documentation, or verified repository behavior.

### Project Convention

A deliberate ChandraMap choice.

### Hypothesis

An idea to be tested.

### Experimental Result

A measured outcome from a documented experiment.

### Planned Research

A future investigation.

Do not present hypotheses or planned research as established fact.

---

## 15.1 Robustness vs Invariance

Use technical wording carefully.

Prefer:

> designed to improve robustness under Sun-angle differences

instead of:

> Sun-angle invariant

unless a precise definition and benchmark evidence support the stronger claim.

Likewise prefer:

> scale-aware

or:

> robust across tested scale differences

instead of unqualified claims of universal scale invariance.

---

## 15.2 Unsupported Superlatives

Avoid unsupported terms such as:

- state-of-the-art
- perfect
- best
- universally invariant
- highly accurate
- industry-leading
- production-grade

Such wording requires appropriate evidence and scope.

---

# 16. Sensor and Dataset Documentation

Sensor documentation should preserve the physical meaning of the data.

### OHRC

High-resolution panchromatic lunar imagery.

Project context commonly references approximately `0.25–0.32 m/px`, depending on source/product documentation.

### TMC-2

Panchromatic terrain imagery.

Project context commonly references approximately `5 m/px`.

Use the canonical name:

> **TMC-2**

### IIRS

Hyperspectral / imaging-infrared data.

Project context commonly references approximately `80 m/px` spatial sampling.

Do not describe IIRS merely as a low-resolution camera.

### LRO NAC

High-resolution reference imagery whose product scale varies.

### LRO WAC

Broader-area reference/context imagery.

These values and descriptions are broad context.

For actual processing:

> **Specific product metadata takes precedence over broad instrument-level approximations.**

---

## 16.1 No False Precision

Do not convert approximate instrument descriptions into universal exact constants.

Avoid:

> OHRC is exactly `0.250000 m/px`.

Prefer wording that preserves uncertainty and product dependence.

Exact numerical values should come from authoritative product metadata where appropriate.

---

# 17. IIRS Documentation

IIRS documentation must preserve its hyperspectral nature.

When discussing conventional 2D image matching, explain that a registration-ready 2D representation may be required.

Potential research representations include:

- selected spectral band
- PCA component
- multi-band composite
- gradient representation
- edge/structural representation

Do not declare one representation universally optimal without controlled evidence.

Do not hard-code one universal band count when actual product metadata should determine it.

---

# 18. Scale and Resolution Documentation

Use `resolution` carefully.

Distinguish among:

- image dimensions
- spatial resolution
- spectral resolution
- GSD

Use `scale` carefully.

Distinguish among:

- physical ground scale
- GSD
- image-resizing factor
- geometric scale factor
- pyramid level

Do not write:

> resolution = 4096

when the intended meaning is image dimensions.

Do not write:

> scale = 5

when the intended meaning is:

```text
5 m/px
```

---

## 18.1 Resampling Language

Upsampling increases raster samples through interpolation.

It does not recreate terrain detail the sensor did not measure.

Avoid wording such as:

> resolution enhanced

when the operation was only interpolation.

Prefer:

> upsampled representation

or:

> resized representation

when that is what actually occurred.

---

# 19. Pipeline Documentation

When documenting the normal ChandraMap scientific pipeline, preserve the intended processing order.

Conceptually:

```text
Candidate Matches
        ↓
Geometric Verification / RANSAC
        ↓
Verified Inliers
        ↓
Sub-Pixel Refinement where supported
        ↓
Final Transform Refit
        ↓
Registration
        ↓
Independent Evaluation
```

Do not accidentally imply:

```text
Candidate Matches
        ↓
Refine Everything
        ↓
RANSAC
```

unless documenting a deliberate alternative experiment.

If tie-point coordinates are refined, documentation should make clear that the final transformation is refitted where the pipeline specification requires it.

---

# 20. Retrieval Documentation

Keep these responsibilities distinct.

### Global Retrieval

Finds candidate reference regions.

### Local Matching

Finds point-level candidate correspondences.

### Geometric Verification

Determines which candidate correspondences are geometrically consistent.

### Registration

Applies the accepted transformation to align imagery.

Do not collapse all four concepts into the vague term:

> image matching

when the distinction matters.

---

## 20.1 FAISS

When FAISS is used or discussed, describe its project role as vector similarity indexing/search.

Conceptually:

```text
Image / Reference Tile
        ↓
Global Descriptor
        ↓
Vector Index / Search
        ↓
Candidate ID
        ↓
Metadata Mapping
        ↓
Reference Candidate
```

Do not describe FAISS as:

- an image feature extractor
- a local matcher
- a geometric verifier
- a registration algorithm

---

## 20.2 ALIKED and LightGlue

Preserve their distinct roles.

```text
ALIKED
→ sparse local feature extraction

LightGlue
→ sparse feature matching
```

Avoid terminology that obscures those responsibilities.

---

## 20.3 LoFTR

Describe LoFTR as a detector-free correspondence/matching method.

Do not describe it merely as a feature extractor.

---

# 21. Benchmark Documentation

Benchmark documentation must contain measured results only when those results actually exist.

Never invent project results such as:

```text
92% confidence
0.4 px RMSE
95% inlier ratio
99% success rate
2× speedup
```

unless they are real measured values from a documented experiment.

---

## 21.1 Illustrative Values

If an example requires synthetic values, label them unmistakably as:

- hypothetical
- illustrative
- example only
- not a measured ChandraMap result

Prefer examples that do not resemble real project claims when realistic numbers are unnecessary.

---

## 21.2 Benchmark V1–V4

Benchmark V1–V4 are research/pipeline configurations.

They are not software release versions.

Write:

> Benchmark V1

rather than ambiguous `v1` where release/version confusion is possible.

Never assume:

```text
Benchmark V1 = software v1.0.0
```

---

## 21.3 Protect V1 Scope

Canonical V1 documentation must remain consistent with [`V1_SCOPE.md`](../context/V1_SCOPE.md).

Do not document later-version capabilities as canonical V1 behavior when V1 scope excludes them.

Examples include:

- FAISS/global retrieval
- LightGlue
- LoFTR
- full native IIRS processing
- DEM-aware geometry
- piecewise warping
- advanced refinement

unless V1 scope is deliberately revised.

---

# 22. Metrics and Accuracy Documentation

Scientific metrics must include enough context to be interpretable.

Bad:

```text
RMSE = 0.7
```

Better, when correct:

```text
RMSE = 0.7 source-image px
```

Documentation should identify, where relevant:

- metric name
- unit
- coordinate domain
- evaluation population
- whether points were used for fitting

---

## 22.1 Sub-Pixel Language

`Sub-pixel` means less than one pixel in the specified image coordinate space.

It does not automatically mean:

> sub-metre

Ground-distance claims require valid physical/geospatial conversion.

---

## 22.2 Fit Points vs Check Points

Document this distinction explicitly.

```text
Fit Points
    ↓
Estimate Transform
```

```text
Independent Check Points
    ↓
Evaluate Final Transform
```

Do not describe fit-point residuals as independent registration accuracy.

---

## 22.3 Visual Validation

Registered overlays and previews are useful diagnostics.

Do not write:

> The registration is accurate because the images visually align.

Visual appearance is not a substitute for quantitative validation.

---

# 23. Failure Documentation

ChandraMap documentation must treat failure and rejection as valid scientific outcomes.

Where implemented, meaningful failure reasons may include:

- invalid input
- unsupported input
- insufficient features
- insufficient candidate matches
- geometric-verification failure
- degenerate transform
- poor spatial coverage
- retrieval failure
- quality rejection

Do not imply that every source/reference pair must produce a successful registration.

Distinguish:

> scientific/pipeline failure

from:

> unexpected software error

where the distinction matters.

---

# 24. Documenting Configuration

Only document configuration that actually exists.

Verify:

- configuration file
- field/key name
- accepted value
- default
- precedence
- required/optional status

before publishing it.

Do not invent plausible configuration such as:

```yaml
matcher: lightglue
confidence: 0.92
```

if the repository does not support those fields.

---

## 24.1 Configuration Examples

Examples must match the actual supported schema.

When a configuration example is intentionally conceptual, label it:

> Conceptual example — not an implemented configuration schema.

Avoid conceptual pseudo-configurations in user-facing setup instructions where readers may mistake them for runnable input.

---

# 25. Documenting Commands

Commands must be verified against the current repository.

Before publishing installation, test, build, benchmark, or development commands, inspect relevant:

- package manifests
- task definitions
- CLI definitions
- scripts
- container configuration
- workflow files

Do not assume commands such as:

```bash
pip install -r requirements.txt
pytest
npm run dev
docker compose up
```

are valid merely because they are common.

---

## 25.1 Command Quality

Documented commands should be:

- complete
- copy-pasteable
- scoped to the correct working directory
- explicit about required prerequisites where necessary

Avoid shell prompts such as `$` when repository style prefers directly copyable commands.

---

## 25.2 Command Output

Do not fabricate output such as:

```text
25 passed
Benchmark complete: RMSE 0.42 px
```

If command output is shown:

- use output captured from real execution when claiming actual behavior
- label purely illustrative output explicitly

Never imply command execution occurred when it did not.

---

# 26. Documenting Repository Paths

Only document real paths as current repository structure.

Do not invent paths merely because they would fit the architecture.

Bad:

```text
src/chandramap/retrieval/faiss.py
```

if that path has not been verified.

For proposed structures, label them clearly:

> Target structure

or:

> Conceptual structure

---

## 26.1 Portable Paths

Prefer repository-relative paths.

Avoid examples tied to a contributor's machine such as:

```text
C:\Users\name\ChandraMap\...
```

or:

```text
/home/name/ChandraMap/...
```

unless the document is explicitly explaining platform-specific behavior.

---

## 26.2 Repository Trees

When showing a repository tree, verify that it reflects current structure.

If the tree is aspirational, label it clearly.

Do not mix current and proposed directories in one unlabeled tree.

---

# 27. API and Contract Documentation

Only document public endpoints or contracts that actually exist.

Where applicable, API documentation should describe:

- method
- path
- purpose
- inputs
- outputs
- errors
- units
- status semantics
- example request/response

Do not invent endpoints.

---

## 27.1 API Examples

Examples must match actual:

- field names
- types
- optionality
- status semantics
- coordinate conventions
- units

Do not create aesthetically pleasing but fictional API schemas.

---

## 27.2 Result Schemas

Scientific result documentation should preserve important semantics such as:

- source identity
- reference identity
- transformation model
- transformation direction
- coordinate domains
- metric units
- result status
- failure semantics

Avoid documenting a bare matrix without explaining what it transforms.

---

# 28. Coordinates, Units, Shapes, and Types

## 28.1 Coordinates

Clarify coordinate conventions where ambiguity exists.

Potential distinctions include:

- `(x, y)`
- `(row, column)`
- source-image pixels
- reference-image pixels
- projected lunar coordinates
- latitude/longitude

Do not assume library conventions are obvious to readers.

---

## 28.2 Units

Use explicit units such as:

- `px`
- `m`
- `m/px`
- `°`
- `rad`

Avoid unlabeled physical values.

---

## 28.3 Array Shapes

Where relevant, document shapes such as:

```text
(H, W)
(H, W, C)
(N, 2)
```

only when those conventions are supported by the implementation.

Do not guess hyperspectral cube axis order.

---

## 28.4 Types

Documentation must match actual code/contracts.

Do not document an input as:

> integer

when the real implementation accepts:

> float or null

Documentation drift is a defect.

---

# 29. Markdown Style

Use clean GitHub-flavored Markdown.

Prefer:

- one clear H1
- hierarchical headings
- compact technical paragraphs
- descriptive lists
- concise tables
- relative repository links
- language-labelled code fences
- simple diagrams where they improve understanding

Avoid:

- excessive HTML
- excessive emojis
- decorative heading levels
- unnecessary nested lists
- giant walls of text
- excessive visual ornamentation

---

## 29.1 Heading Structure

A standalone Markdown document should normally have one H1.

Example:

```markdown
# ChandraMap Documentation Rules
```

Then use H2/H3 headings hierarchically.

Avoid skipping heading levels without a clear reason.

---

## 29.2 Title Consistency

The title should match the document responsibility.

Examples:

```text
SYSTEM_OVERVIEW.md
→ # ChandraMap System Overview

DATA_FLOW.md
→ # ChandraMap Data Flow

DOCUMENTATION_RULES.md
→ # ChandraMap Documentation Rules
```

---

## 29.3 Paragraphs and Lists

Use compact paragraphs for explanation.

Use lists when items are genuinely parallel.

Do not convert every sentence into a bullet.

Keep nested lists shallow where possible.

---

## 29.4 Tables

Tables work well for:

- comparisons
- responsibility maps
- status matrices
- terminology mappings
- benchmark-version differences

Avoid very wide or very long tables when prose would be clearer.

---

## 29.5 Code Fences

Use ordinary Markdown code fences with a language identifier where useful.

Example:

```python
def register(source, reference):
    ...
```

For conceptual text diagrams:

```text
Source
  ↓
Reference Matching
  ↓
Registration
```

Do not add renderer-specific code-fence attributes or metadata unless repository tooling explicitly requires them.

---

# 30. Examples and Pseudocode

Examples should clearly identify whether they are:

- executable
- simplified
- conceptual
- pseudocode
- hypothetical

Do not present pseudocode as runnable code.

Executable examples should be verified against the repository where practical.

---

## 30.1 Scientific Examples

Scientific examples must not accidentally create fake project results.

Prefer:

```text
Example structure:
RMSE: <measured value> <unit>
```

over invented realistic-looking values when actual measurements are unavailable.

---

## 30.2 Example Data

If examples use:

- filenames
- product IDs
- coordinates
- metadata
- API results

do not imply they are real mission products unless they actually are.

Clearly label fictional example data.

---

# 31. Diagrams

Diagrams should simplify accurate information, not replace it.

A diagram should:

- use canonical terminology
- preserve stage ordering
- show meaningful boundaries
- match the surrounding text
- indicate whether it is current, target, experimental, or conceptual when necessary

Do not create a visually attractive diagram that contradicts the documented pipeline.

---

## 31.1 Architecture Diagram Status

A target architecture diagram should be introduced as:

> Target architecture

or:

> Conceptual target flow

not:

> Current implementation

unless verified.

---

## 31.2 Scientific Pipeline Diagrams

Preserve important distinctions such as:

```text
Global Retrieval
      ↓
Candidate Region
      ↓
Local Matching
      ↓
Candidate Matches
      ↓
Geometric Verification
      ↓
Verified Inliers
```

Do not merge independent responsibilities solely to make a diagram smaller.

---

## 31.3 Diagram Technology

Use the repository's established diagram format where one exists.

Do not introduce a dependency on a specific diagram renderer merely because it is convenient.

Plain-text diagrams are acceptable when they communicate the structure clearly.

---

# 32. Links

Prefer repository-relative links for internal documentation.

Example:

```markdown
[Pipeline](../architecture/PIPELINE.md)
```

Benefits include:

- repository portability
- branch/fork compatibility
- easier local navigation

---

## 32.1 Verify Links

Before completing documentation changes, check relevant links where practical.

Do not knowingly add:

- broken relative links
- links to nonexistent files
- outdated anchors
- obsolete external resources

---

## 32.2 Stable External Links

Prefer stable authoritative pages over:

- temporary search-result URLs
- personal mirrors
- URL shorteners
- transient download links

For mission/product documentation, prefer the responsible agency/archive when possible.

---

# 33. Citations and External References

Scientific claims should use authoritative sources where appropriate.

Preferred evidence includes:

1. official mission/instrument documentation
2. official data/archive documentation
3. peer-reviewed publications or original method papers
4. official library/project documentation
5. reputable secondary sources when primary sources are unavailable

Do not cite an informal blog for a technical claim when an authoritative primary source is available.

---

## 33.1 Citation Purpose

Citations are especially useful for:

- instrument specifications
- dataset/product characteristics
- algorithm descriptions
- scientific assumptions
- external benchmark definitions
- research claims

Internal project conventions generally need project-document references rather than external scientific citations.

---

## 33.2 Citation Accuracy

A citation must actually support the claim beside it.

Do not attach a source merely because it discusses the same broad topic.

Do not invent:

- DOI
- paper title
- author list
- publication year
- URL

Verify bibliographic information before adding it.

---

## 33.3 Separate External Fact from Project Choice

For example:

> Published documentation describes the sensor characteristics as X.

is an external fact.

> ChandraMap therefore uses Y preprocessing route.

is a project design choice.

Do not blur the two.

---

# 34. Screenshots and Visual Documentation

Screenshots should support documentation, not become its source of truth.

Use screenshots when they materially help explain:

- UI state
- workflow
- visualization output
- artifact interpretation

Avoid screenshots where text or structured examples communicate the same information more maintainably.

---

## 34.1 Screenshot Accuracy

Screenshots should reflect the documented interface version.

When the UI changes materially:

- update the screenshot
- remove it
- clearly label it as historical

Stale screenshots create documentation drift.

---

## 34.2 Scientific Screenshots

A registered overlay or match visualization may illustrate a result.

It must not be used as the only evidence that registration is accurate.

Where scientific quality is being claimed, measured evidence should accompany the visual where available.

---

## 34.3 Sensitive Information

Before publishing screenshots, check for:

- credentials
- API keys
- user-specific paths
- private datasets
- personal information
- internal URLs
- tokens

Do not publish sensitive information accidentally.

---

# 35. Research Documentation

Research documentation should distinguish:

- research question
- hypothesis
- method
- data
- configuration
- result
- limitation
- failure
- conclusion

Negative results are legitimate research outcomes.

Do not rewrite failed experiments as successes.

---

## 35.1 Experimental Status

Experimental methods should be clearly labeled.

A successful notebook experiment on one image pair does not automatically become:

> supported ChandraMap functionality

Promotion from research to supported functionality requires implementation evidence appropriate to the project.

---

## 35.2 Reproducibility

Where practical, experimental documentation should identify:

- dataset/pair
- configuration
- method/model
- checkpoint where relevant
- code revision
- metric definition
- random seed where relevant
- runtime environment where relevant

Do not claim an experiment is reproducible if essential information is missing.

---

# 36. Release, Changelog, and Roadmap Documentation

## 36.1 Release Documentation

Document only releases that actually exist.

Do not invent:

- semantic versions
- release dates
- release notes
- compatibility guarantees

Follow the repository's actual release process.

---

## 36.2 Changelog Entries

A useful changelog entry should describe a notable completed change.

Avoid copying every commit message into the changelog.

Do not place future plans in a completed release section.

---

## 36.3 Roadmap Wording

Roadmap language should clearly express future intent.

Prefer:

- planned
- target
- proposed
- under investigation

Avoid present-tense capability claims for incomplete work.

---

# 37. Generated Documentation

If documentation is generated from:

- schemas
- code
- API definitions
- configuration
- templates

identify the authoritative source and generator.

Do not manually edit generated output when the generator/source should be changed instead.

Where useful, generated files should indicate that they are generated.

Do not assume the repository has a generated-documentation system unless verified.

---

# 38. Documentation Review Workflow

Before completing a documentation change:

1. identify the document's audience and responsibility
2. identify the authoritative technical/scientific sources
3. inspect relevant implementation where claims are repository-specific
4. update the smallest authoritative document
5. remove or update conflicting duplicated information
6. verify current/target/experimental/planned language
7. verify terminology
8. verify commands
9. verify paths
10. verify configuration examples
11. verify units and coordinates
12. verify scientific claims
13. verify links and cross-references
14. verify diagrams match the text
15. verify examples are real or clearly labelled
16. verify no fake benchmark values were introduced
17. verify Markdown structure and readability

Documentation review is part of correctness, not a final cosmetic step.

---

## 38.1 Change Impact

When implementation changes, inspect whether documentation is affected.

Potential high-impact changes include:

- public APIs
- CLI commands
- configuration
- repository paths
- pipeline order
- result schemas
- coordinate conventions
- metric definitions
- benchmark scope
- dataset support
- dependencies
- release behavior

Documentation should be updated in the same change where practical.

---

# 39. Documentation Maintenance

Documentation must evolve with the repository.

Update documentation when:

- documented behavior changes
- implementation status changes
- paths move
- commands change
- configuration changes
- API contracts change
- benchmark methodology changes
- dataset handling changes
- terminology changes
- architecture responsibilities move

Do not update unrelated documentation merely to create churn.

---

## 39.1 Documentation Drift

Documentation drift is a bug.

Examples include:

- command no longer exists
- path was renamed
- endpoint was removed
- field type changed
- architecture diagram shows old module ownership
- planned feature is now implemented but still labelled planned
- removed feature remains documented as supported

Treat meaningful drift as something to fix.

---

## 39.2 Historical Documentation

Do not rewrite historical release/changelog information merely to match current behavior.

Historical documentation should describe the project at that historical point.

Current documentation should describe current behavior.

Keep those responsibilities separate.

---

# 40. Documentation Testing and Verification

Where repository tooling exists, documentation changes may be checked through:

- Markdown linting
- link validation
- documentation builds
- example execution
- schema-generated reference checks

Only document or invoke tooling actually configured by the repository.

Do not invent documentation commands.

---

## 40.1 Execution Claims

Never claim:

> commands verified

unless they were actually executed.

Never claim:

> links checked

unless they were actually checked.

Never claim:

> examples tested

unless they were actually tested.

If verification was not performed, report:

> Not run

rather than implying success.

---

# 41. Documentation Anti-Patterns

## 41.1 Aspirational Documentation Presented as Current

Bad:

> ChandraMap performs whole-Moon FAISS retrieval.

when the capability is only planned.

---

## 41.2 Copy-Paste Duplication

Repeating the same scientific explanation across five files creates drift.

Use one authoritative document and cross-reference it.

---

## 41.3 Giant README

Do not force architecture, datasets, research methodology, development rules, and complete API documentation into `README.md`.

---

## 41.4 Fabricated Commands

Never invent setup, test, build, benchmark, or deployment commands.

---

## 41.5 Fabricated Paths

Do not document files or directories that have not been verified.

---

## 41.6 Fake Benchmark Results

Never invent RMSE, accuracy, confidence, success rate, runtime, or speedup values.

---

## 41.7 Unsupported Superlatives

Avoid unproven claims such as:

> state-of-the-art

or:

> perfectly invariant

---

## 41.8 Ambiguous Units

Avoid:

```text
RMSE = 0.8
```

without units and context.

---

## 41.9 Ambiguous Coordinates

Avoid documenting a point simply as:

```text
(100, 200)
```

when readers need to know:

- `(x, y)` or `(row, column)`
- source or reference image
- pixel or map coordinates

---

## 41.10 Bare Transform Matrix

Do not show a transformation matrix without direction when direction matters.

---

## 41.11 Candidate-as-Verified

Do not call matcher outputs verified correspondences before geometric verification.

---

## 41.12 RANSAC-Inlier-as-Ground-Truth

Model-consistent inliers are not automatically independent truth.

---

## 41.13 Visual-Only Accuracy Claims

An aligned-looking overlay does not establish measured registration accuracy.

---

## 41.14 Target/Current Mixing

Do not mix target and current architecture in one unlabeled diagram or table.

---

## 41.15 Fake API Documentation

Do not invent endpoints, fields, error responses, or examples.

---

## 41.16 Stale Screenshots

Do not preserve screenshots that materially contradict the current UI.

---

## 41.17 Dead Links

Do not add cross-references without checking that the target exists when possible.

---

## 41.18 Renderer-Specific Markdown Noise

Do not add nonstandard metadata to headings or code fences unless project tooling explicitly requires it.

---

## 41.19 Documentation as Implementation Evidence

A feature being described in a document does not prove it is implemented.

Verify code/configuration/tests.

---

## 41.20 Fake Verification Claims

Do not say documentation, commands, tests, links, examples, or benchmarks were verified unless that verification actually occurred.

---

# 42. Documentation Review Checklist

Before accepting documentation work, verify:

- [ ] The document has one clear responsibility.
- [ ] The intended audience is clear.
- [ ] Repository-specific claims were checked against appropriate sources where possible.
- [ ] Current functionality is not confused with target architecture.
- [ ] Implemented, tested, documented, experimental, planned, proposed, supported, deprecated, and removed are used accurately.
- [ ] No planned feature is presented as implemented.
- [ ] No documented-only feature is presented as implemented.
- [ ] The document does not unnecessarily duplicate another authoritative file.
- [ ] Canonical terminology is used.
- [ ] `TMC-2` is used when specifically referring to the Chandrayaan-2 instrument.
- [ ] IIRS is described as hyperspectral / imaging-infrared data.
- [ ] Product-specific metadata remains authoritative for exact processing values.
- [ ] Approximate sensor specifications are not presented as universal exact constants.
- [ ] Upsampling is not described as physical detail recovery.
- [ ] Global retrieval and local matching are distinguished.
- [ ] FAISS is described as vector indexing/search rather than local image matching.
- [ ] ALIKED and LightGlue roles remain distinct.
- [ ] LoFTR is described as detector-free correspondence/matching.
- [ ] Candidate matches are not described as verified inliers prematurely.
- [ ] RANSAC inliers are not described as independent ground truth.
- [ ] Pipeline diagrams preserve the correct verification/refinement/refit ordering.
- [ ] V1 documentation remains consistent with `V1_SCOPE.md`.
- [ ] Benchmark V1–V4 are not confused with software releases.
- [ ] No benchmark values were fabricated.
- [ ] No confidence percentage was fabricated.
- [ ] Metrics include units and context where relevant.
- [ ] Sub-pixel is not equated automatically with sub-metre.
- [ ] Fit points and independent check points are distinguished.
- [ ] Visual alignment is not presented as proof of accuracy.
- [ ] Failure/rejection is documented as a valid outcome where relevant.
- [ ] Configuration fields shown actually exist or are clearly conceptual.
- [ ] Commands were verified before being presented as runnable.
- [ ] No fake command output is included.
- [ ] Repository paths shown as current actually exist.
- [ ] Conceptual/target trees are labelled.
- [ ] API endpoints and schemas are not invented.
- [ ] Coordinate conventions are explicit where needed.
- [ ] Units are explicit.
- [ ] Array shapes are not guessed.
- [ ] Internal links are valid where checked.
- [ ] External references support the claims beside them.
- [ ] Scientific citations prefer authoritative sources.
- [ ] Screenshots do not expose secrets or private information.
- [ ] Screenshots reflect the documented interface where applicable.
- [ ] Generated documentation is not manually edited against its source-of-truth mechanism.
- [ ] Markdown heading levels are coherent.
- [ ] Code fences use clean standard Markdown.
- [ ] Examples are identified as executable, conceptual, or hypothetical.
- [ ] No unsupported superlative or invariance claim remains.
- [ ] No test/command/benchmark execution is claimed without evidence.
- [ ] Documentation remains concise enough for its audience.

---

# 43. Related Documentation

Use the following ChandraMap documents where relevant.

[`../ENGINEERING_RULES.md`](../ENGINEERING_RULES.md)
→ repository-wide engineering behavior

[`CODING_RULES.md`](CODING_RULES.md)
→ source-code standards

[`TESTING_RULES.md`](TESTING_RULES.md)
→ software and scientific testing standards

[`../context/PROJECT_CONTEXT.md`](../context/PROJECT_CONTEXT.md)
→ project identity and scope

[`../context/DOMAIN_CONTEXT.md`](../context/DOMAIN_CONTEXT.md)
→ scientific/domain constraints

[`../context/TERMINOLOGY.md`](../context/TERMINOLOGY.md)
→ canonical vocabulary

[`../context/DATASETS.md`](../context/DATASETS.md)
→ dataset/product context and provenance

[`../context/V1_SCOPE.md`](../context/V1_SCOPE.md)
→ canonical Benchmark V1 boundary

[`../architecture/SYSTEM_OVERVIEW.md`](../architecture/SYSTEM_OVERVIEW.md)
→ high-level system architecture

[`../architecture/PIPELINE.md`](../architecture/PIPELINE.md)
→ scientific processing order

[`../architecture/MODULE_MAP.md`](../architecture/MODULE_MAP.md)
→ repository responsibility ownership

[`../architecture/DATA_FLOW.md`](../architecture/DATA_FLOW.md)
→ scientific information movement and semantics

When additional benchmark, metrics, API, configuration, or research documents exist, reference their actual paths rather than inventing them.

---

# 44. Key Documentation Rules for AI Agents

1. Document repository reality, not what would be convenient for the project to have.

2. Inspect implementation before making repository-specific claims whenever possible.

3. Do not invent files, modules, classes, endpoints, schemas, configuration fields, commands, or dependencies.

4. Distinguish current, target, experimental, planned, and proposed behavior.

5. `Documented` does not mean `Implemented`.

6. `Tests exist` does not mean `Tested`.

7. Never claim tests, commands, examples, CI, or benchmarks succeeded without execution evidence.

8. Use one authoritative document per major responsibility and cross-reference it.

9. Do not duplicate detailed scientific explanations unnecessarily.

10. Keep root/human-facing documentation distinct from `.ai/` agent-oriented context.

11. Keep `README.md` concise enough to remain a useful entry point.

12. Use `ROADMAP.md` for future intent, not current capability claims.

13. Use `CHANGELOG.md` for notable completed changes, not future plans.

14. Use canonical ChandraMap terminology.

15. Use `TMC-2` when specifically referring to Terrain Mapping Camera-2.

16. Describe IIRS as hyperspectral / imaging-infrared data.

17. Do not present broad instrument approximations as exact product constants.

18. Product metadata takes precedence for actual product-specific values.

19. Do not describe upsampling as recovery of physical terrain detail.

20. Distinguish global retrieval from local matching.

21. Describe FAISS as vector indexing/search, not an image feature extractor or local matcher.

22. Preserve the distinction between ALIKED feature extraction and LightGlue matching.

23. Describe LoFTR as detector-free correspondence/matching.

24. Use `candidate match` before geometric verification.

25. Use `verified inlier` after geometric verification.

26. Do not call RANSAC inliers independent ground truth automatically.

27. Preserve the verified-inlier → refinement → transform-refit pipeline order where applicable.

28. Do not present a registered preview as proof of accuracy.

29. Always include metric units and coordinate context when needed.

30. Do not equate sub-pixel with sub-metre.

31. Distinguish fit points from independent check points.

32. Benchmark V1–V4 are not software release versions.

33. Keep canonical V1 documentation consistent with `V1_SCOPE.md`.

34. Never fabricate RMSE, accuracy, inlier ratio, success rate, confidence, runtime, or speedup.

35. Label hypothetical values unmistakably if examples require them.

36. Do not invent configuration examples.

37. Do not invent shell commands.

38. Do not invent command output.

39. Do not invent repository paths.

40. Label conceptual/target repository trees clearly.

41. Do not invent API endpoints or result fields.

42. Document transformation direction where it matters.

43. Document coordinate ordering where it matters.

44. Use explicit scientific units.

45. Do not guess hyperspectral array layout.

46. Prefer clean GitHub Markdown with one H1 and coherent heading hierarchy.

47. Use standard code fences without nonstandard metadata unless repository tooling requires it.

48. Prefer relative links for repository documentation.

49. Verify internal links where practical.

50. Prefer authoritative sources for mission, dataset, algorithm, and library claims.

51. Ensure citations actually support the claims they accompany.

52. Distinguish external scientific facts from ChandraMap design choices.

53. Keep screenshots synchronized with the documented interface.

54. Do not expose secrets or private information in documentation assets.

55. Treat failure and rejection as valid scientific outcomes.

56. Do not describe experimental code as supported merely because one example works.

57. Update documentation when public behavior, configuration, schemas, paths, metrics, or architecture materially change.

58. Treat documentation drift as a correctness defect.

59. Keep historical documentation historically accurate instead of rewriting it to describe the present.

60. When uncertain, document the uncertainty instead of inventing an answer.

<!-- Source specification: :contentReference[oaicite:0]{index=0} -->
