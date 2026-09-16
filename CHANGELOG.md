# Changelog

All notable changes to ChandraMap will be documented in this file.

This changelog follows the structure of [Keep a Changelog](https://keepachangelog.com/en/1.1.0/)
and uses semantic release conventions where applicable (see
[Semantic Versioning](https://semver.org/)). ChandraMap has not yet committed
to a stable public API or strict SemVer guarantees; version numbers below
`1.0.0` should be read as active research/development releases in which
interfaces, formats, and benchmark protocols may still change.

**Software releases vs. benchmark configurations.** ChandraMap distinguishes
between two kinds of versioning that are easy to conflate in a research
repository:

- **Software releases** (e.g. `v0.1.0`, `v0.2.0`, `v1.0.0`) — track the state
  of the codebase, its interfaces, and its documentation.
- **Benchmark configurations** (e.g. Benchmark V1, Benchmark V2, Benchmark V3,
  Benchmark V4) — track experimental/research pipeline configurations used to
  evaluate and compare registration approaches. A benchmark configuration
  number does **not** correspond to a software release number unless a
  changelog entry explicitly says so.

**Reproducibility note.** Where a change alters evaluation methodology,
dataset composition, ground truth, or matcher configuration in a way that
affects experimental validity, the relevant entry will say so explicitly,
including whether resulting metrics remain comparable to prior results.

## [Unreleased]

This project is currently under active research and development. No software
version has been tagged yet. Entries below reflect the current state of
planning and documentation for the project as established in this repository's
initial scope; they will be reclassified into a dated release once a first
tagged version is cut.

### Added

- Documented the project scope and problem context (SIH 26166): multi-modal,
  sun-angle and scale invariant image correspondence using Chandrayaan-2
  optical/scientific imagery.
- Documented the intended high-level pipeline, from input lunar imagery
  through sensor-aware preprocessing, candidate retrieval, local feature
  matching, geometric verification, sub-pixel refinement, registration, and
  independent quality evaluation.
- Documented sensor-specific characteristics and handling requirements for
  OHRC, TMC-2, and IIRS source imagery, and for LRO NAC and LRO WAC reference
  imagery.
- Documented the planned four-version benchmark architecture (Benchmark V1
  through Benchmark V4) as a set of research configurations for comparing
  registration approaches, distinct from software release versioning.
- Documented the intended scope of evaluation outputs, including RMSE,
  inlier count, inlier ratio, spatial coverage, retrieval metrics, runtime,
  and explicit failure reporting, as the project's primary scientific
  deliverables (rather than a lunar mosaic or map UI, which is considered a
  downstream demonstration).

### Changed

- N/A

### Deprecated

- N/A

### Removed

- N/A

### Fixed

- N/A

### Security

- N/A

<!--
Template for future releases. Do not treat this as an existing release.

## [0.1.0] - YYYY-MM-DD

### Added
- ...

### Changed
- ...

### Fixed
- ...
-->

## Maintaining this changelog

1. Add notable changes under `[Unreleased]`, in the correct category
   (`Added`, `Changed`, `Deprecated`, `Removed`, `Fixed`, `Security`).
2. Do not record every commit — only changes with user-, developer-, or
   research-facing impact.
3. On release: create a new dated version heading, move the relevant
   `Unreleased` entries under it, and reset `Unreleased`.
4. Add issue/PR references only when they actually exist.
5. Preserve historical entries; do not rewrite past releases without cause.
6. When a change affects experimental validity (metric definitions, dataset
   splits, ground truth, matcher configuration, evaluation-point selection),
   state explicitly whether resulting benchmark numbers remain comparable to
   prior results.
7. Mark breaking changes clearly within their category using a
   **Breaking:** prefix.
8. Use `Deprecated` when functionality still exists but is scheduled for
   removal; use `Removed` only once it is actually gone, as a separate entry.
9. Keep future plans in `ROADMAP.md`, not here — this file records only
   changes that have actually happened.
