# Contributing to ChandraMap

Thank you for your interest in contributing to ChandraMap, an open-source
research and engineering project for lunar image correspondence and
registration. This guide explains how to contribute effectively, whether
you're fixing a bug, adding a test, writing documentation, running a
benchmark, or proposing new research.

ChandraMap treats scientific validity and reproducibility as seriously as
code quality. This guide is written so that contributors from many
backgrounds — software engineering, computer vision, remote sensing,
MLOps, or planetary science — can understand what a good contribution looks
like here, even without prior lunar-imaging experience.

## Code of Conduct

All contributors are expected to follow the project's
[Code of Conduct](CODE_OF_CONDUCT.md). This document does not repeat it.

## Before You Start

- Skim [`README.md`](README.md) to understand what ChandraMap currently is
  and how it's used.
- Skim [`ROADMAP.md`](ROADMAP.md) to understand planned direction, especially
  before starting a large change.
- Check [`CHANGELOG.md`](CHANGELOG.md) for recent notable changes.
- For anything security-sensitive, see [`SECURITY.md`](SECURITY.md) instead
  of opening a public issue.
- If the repository includes `AGENTS.md` or an `.ai/` directory and you are
  using a coding agent, follow those repository-specific instructions in
  addition to this guide.

Contributions do not need to appear in the roadmap to be accepted, but
significant architectural or research direction changes should be checked
against `ROADMAP.md` and, where the repository supports it, discussed in an
issue before a large implementation effort begins.

## Ways to Contribute

### Code

Core registration pipeline, preprocessing, feature extraction, feature
matching, geometric verification, sub-pixel refinement, evaluation,
retrieval, backend/API, visualization, CLI, and general tooling.

### Research

Matcher comparisons, sensor-specific processing, illumination invariance,
multimodal registration, IIRS representations, DEM-assisted registration,
uncertainty estimation, and related experimental directions.

### Benchmarking

New benchmark cases, evaluation scripts, metric implementations, regression
tests, and stress-test cases.

### Testing

Unit tests, integration tests, regression tests, and failure tests.

### Documentation

Architecture documentation, dataset guides, tutorials, API docs,
terminology, and benchmark specifications.

### Data Tooling

Download helpers, metadata parsers, format readers, dataset manifests, and
validation tools.

### UI / Visualization

Match visualization, registered overlays, metric dashboards, residual
visualization, and lunar map layers.

## Project Principles

1. Correctness before complexity.
2. Reproducibility before impressive screenshots.
3. Measure improvements against a baseline.
4. Do not fabricate scientific precision.
5. Keep algorithms replaceable and benchmarkable.
6. Preserve sensor-specific behavior where physically necessary.
7. Do not force registrations when confidence is low.
8. Keep failures visible rather than hidden.
9. Prefer maintainable code over unnecessary abstraction.
10. Document assumptions.

## Repository Overview

ChandraMap's repository is organized approximately as follows. Use this to
decide where a change belongs; consult the actual repository tree for the
authoritative layout, since this list may not reflect every directory.

```
ChandraMap/
│
├── .github/          CI workflows, issue/PR templates (if present)
├── benchmarks/        Benchmark configurations, results, and fixtures
├── configs/            Configuration files
├── data/                Dataset manifests, small fixtures (not raw datasets)
├── docs/                Project documentation
├── experiments/         Exploratory research work
├── notebooks/            Research/analysis notebooks
├── research/              Research proposals and in-progress research code
├── results/                Benchmark and experiment outputs
├── scripts/                  Developer and dataset tooling scripts
├── services/                  Backend services (if present)
├── src/chandramap/              Core library code
├── tests/                        Unit, integration, and regression tests
│
├── README.md
├── LICENSE
├── CHANGELOG.md
├── ROADMAP.md
├── CONTRIBUTING.md
├── CODE_OF_CONDUCT.md
├── SECURITY.md
├── CITATION.cff
├── AGENTS.md
├── .gitignore
├── .env.example
└── pyproject.toml
```

If your change doesn't clearly fit one of these areas, ask in your issue or
draft PR rather than guessing.

## Development Setup

### Prerequisites

Use the Git, Python, Node.js, and/or Docker versions defined by the
repository's own configuration (for example `pyproject.toml`,
`.python-version`, `package.json`, Dockerfiles, or CI workflow files) rather
than any version numbers assumed elsewhere. If you can't find an explicit
version requirement, use a current, supported version of the relevant
toolchain.

### Clone / Fork

External contributors should fork the repository, clone their fork, and work
from a branch there. Repository collaborators may clone the repository
directly and work from a branch. In both cases, changes reach `main` only
through a pull request — direct commits to `main` are not part of the
workflow.

### Environment Configuration

Install project dependencies using the package manager(s) actually
configured in the repository (for example, the tool indicated by the
presence of `pyproject.toml`, a lockfile, or a `Makefile` target). If you're
unsure which tool the project uses, check for a lockfile or a documented
setup target before running an installation command.

If a `.env.example` file exists:

1. Copy it to a local `.env`.
2. Fill in the values you need for local development.
3. Never commit `.env` or any file containing real secrets.

### Verify the Setup

After installing dependencies, run the project's test suite and any linting
or formatting checks configured in the repository (for example, via a
`Makefile` target or CI workflow) to confirm your environment is set up
correctly before making changes.

## Development Workflow

### Create a Branch

Branch from the current default branch using a descriptive name (see
[Branch Naming](#branch-naming)).

### Make Changes

Keep changes focused on a single purpose. Follow existing project
architecture and conventions rather than introducing a parallel style.

### Test Changes

Run relevant unit, integration, and/or regression tests locally. Add new
tests for new behavior (see [Testing](#testing)).

### Update Documentation

Update any documentation that your change affects — inline docstrings,
`docs/`, or root-level files — so documentation continues to match actual
behavior.

### Commit Changes

Make focused, well-described commits (see
[Commit Messages](#commit-messages)).

## Branch Naming

Suggested prefixes:

- `feature/<short-description>`
- `fix/<short-description>`
- `docs/<short-description>`
- `test/<short-description>`
- `benchmark/<short-description>`
- `research/<short-description>`
- `refactor/<short-description>`
- `chore/<short-description>`

Examples: `feature/iirs-preprocessing`, `fix/ransac-empty-inliers`,
`benchmark/v1-registration-suite`, `docs/dataset-acquisition`,
`research/illumination-invariance`.

## Commit Messages

ChandraMap recommends a [Conventional Commits](https://www.conventionalcommits.org/)-inspired
style, though it is not strictly enforced unless the repository's tooling
requires it:

```
feat: add TMC-2 preprocessing configuration
fix: reject degenerate homography estimates
test: add registration failure cases
docs: document LROC dataset workflow
benchmark: add V1 scale stress pair
research: add IIRS PCA experiment
refactor: separate retrieval and local matching modules
chore: update development dependencies
```

Common types: `feat`, `fix`, `docs`, `test`, `refactor`, `perf`, `benchmark`,
`research`, `build`, `ci`, `chore`.

Avoid vague messages such as `update`, `changes`, `final`, `fix stuff`, or
`working now` — commit messages should explain intent, not just that
something changed.

Additional expectations:

- Keep commits focused; avoid unrelated changes in the same commit.
- Avoid committing generated files unless the repository intentionally
  tracks them.
- Don't mix large formatting-only changes with functional changes.
- Remove temporary/debug code and accidental binaries before committing.
- Review your diff before committing.

## Coding Standards

Follow the linters, formatters, and type checkers actually configured in the
repository. If a tool isn't configured, don't assume it's required.

### Python

- Use clear type hints where appropriate.
- Prefer small, focused functions.
- Handle errors explicitly rather than suppressing them.
- Use meaningful names and docstrings for important public APIs.
- Avoid hidden global state.
- Use `pathlib` for filesystem paths where suitable.
- Maintain deterministic behavior where practical (especially for anything
  feeding a benchmark).

### TypeScript / Frontend

- Keep component boundaries clear.
- Use typed interfaces.
- Prefer reusable, accessible UI components.
- Keep state handling predictable.
- Avoid unnecessary dependencies.

### General Engineering Rules

- Follow existing project architecture.
- Reuse existing utilities before adding duplicates.
- Keep modules focused; avoid premature abstraction.
- Prefer explicit configuration over hard-coded parameters.
- Handle failures rather than suppressing them.

### Dependency Policy

Before adding a dependency, consider:

- Is it actually required, or can existing dependencies solve the problem?
- Is it actively maintained, and is its license compatible?
- Is it appropriate for research reproducibility?
- How large is it, and does it introduce GPU/CUDA requirements?
- Does it affect CI runtime?
- Is it optional or core to the pipeline?

Justify significant new dependencies in your pull request rather than adding
them to save a few lines of code.

### AI/ML Model Dependencies

For learned models (for example LightGlue, ALIKED, or LoFTR), document:

- model/version and checkpoint source
- license
- expected hardware and CPU/GPU behavior
- required dependencies and configuration
- preprocessing assumptions

Do not have basic repository import silently download large model weight
files.

## Testing

### Unit Tests

For preprocessing functions, feature utilities, metadata parsing,
coordinate conversion, transformation utilities, metrics, and validation
logic.

### Integration Tests

For known image-pair registration, retrieval → local matching flows,
serialization, and CLI/API workflows.

### Regression Tests

Protect previously working benchmark behavior from silently degrading.

### Failure Tests

Cover cases such as: blank image, corrupted input, unsupported format,
unsupported sensor, insufficient matches, zero inliers, degenerate
homography, no overlap, missing metadata, invalid coordinates, or
unavailable model weights.

Not every research experiment needs to become a production unit test, but
any code promoted from `research/` or `experiments/` into the core pipeline
should gain appropriate tests at the time of promotion.

## Scientific Contributions

ChandraMap is a research project, and scientific integrity is treated as a
hard requirement, not a nice-to-have.

### Scientific Integrity Rules

Contributors must **not**:

- invent or manually modify benchmark results
- hide failed examples or cherry-pick only successful image pairs
- claim upsampling recovered spatial detail not present in the source
  sensor's measurements
- claim ground accuracy in metres without valid GSD, projection, and
  reference information
- describe candidate matches as geometrically verified before RANSAC (or
  equivalent) verification has actually run
- claim a pretrained model is lunar-invariant without testing it on lunar
  data
- use fit-point RMSE as though it were independent validation
- claim an algorithm is "better" without a controlled, documented comparison
- remove difficult benchmark cases simply because they lower a metric
- silently change benchmark definitions

Scientific claims must be supported by reproducible experiments, documented
configurations, metrics, benchmark cases, and an appropriate baseline.

### Sensor Terminology

Use `OHRC`, `TMC-2`, `IIRS`, `LRO NAC`, and `LRO WAC`. Do not shorten
`TMC-2` to `TMC` when referring to the Chandrayaan-2 instrument. Describe
IIRS accurately as hyperspectral / imaging infrared data, not as an ordinary
low-resolution camera. OHRC, TMC-2, and IIRS may require different
processing routes — don't assume one preprocessing path fits all three.

### Geometry and Scale Rules

Where relevant, preserve this order:

```
Candidate Matches
      ↓
RANSAC / Initial Model
      ↓
Verified Inliers
      ↓
Sub-Pixel Tie-Point Refinement
      ↓
Final Transform Refit
      ↓
Registration
      ↓
Independent Evaluation
```

Do not refine arbitrary candidate matches before geometric verification, and
don't let flexible warping hide weak correspondences.

Upsampling increases pixel count; it does not recover spatial information
the source sensor never measured. When handling large resolution
differences, use reference pyramids, compare imagery at physically
meaningful effective scales, and refine only where the source data actually
supports that level of detail.

### Benchmark Changes

Benchmark-affecting contributions should document: benchmark
version/configuration, image pairs used, dataset source, preprocessing,
matcher configuration, geometric model, evaluation method, metric
definitions, relevant hardware/runtime context, and random seed where
applicable.

If benchmark methodology changes, explain explicitly whether new results
remain directly comparable with previous results.

Avoid unqualified claims like "20% better." A benchmark claim should be able
to answer: Compared with what? On which image pairs? Using which
configuration and metric? How was ground truth defined? Were the same cases
used for both methods? Was evaluation independent of the points used to fit
the transform?

### Research Contributions

Research work typically starts in `research/`, `experiments/`, `notebooks/`,
or another repository-designated experimental area, and should not
automatically modify the stable/core pipeline. A research contribution
should ideally include: a research question, hypothesis, method,
configuration, dataset/pairs used, baseline, evaluation metric, results,
limitations, failure examples, and reproducibility instructions.

Once evidence is strong enough, research functionality may be proposed for
promotion into `src/chandramap/` or another stable module, gaining tests and
documentation at that point.

### Notebooks

Keep notebooks reproducible: use deterministic inputs where possible, avoid
embedding large data, remove unnecessary outputs when appropriate, and
explain what the notebook demonstrates. Move reusable logic into normal
modules rather than leaving core functionality implemented only in a
notebook.

## Dataset Contributions

Do not commit large lunar datasets into Git. Prefer download instructions,
manifests, metadata, checksums, dataset scripts, or small, clearly licensed
sample fixtures. Raw/large data normally stays outside Git history.

Before redistributing external data, verify its license, the source
agency's data policy, attribution requirements, and redistribution rules.

Where possible, preserve data provenance: mission, instrument, product
identifier, source, acquisition/download method, product level, projection,
GSD/pixel scale, and other relevant metadata. Never relabel data in a way
that obscures its origin.

## Documentation Contributions

Documentation changes are first-class contributions. You may improve
`README.md`, architecture docs, pipeline docs, dataset docs, benchmark docs,
evaluation docs, API docs, CLI docs, terminology, tutorials, examples, and
diagrams.

Documentation should describe actual behavior. Do not document proposed or
planned functionality as though it is already implemented — that belongs in
`ROADMAP.md`.

## Bug Reports

A good bug report includes: a clear title, your environment (OS,
Python/Node version where relevant), the ChandraMap version or commit, the
input type, relevant sensor/product information, steps to reproduce,
expected vs. actual behavior, logs, and a minimal example. Screenshots help
only where genuinely useful.

For registration-specific bugs, include where possible: source/reference
image type, dimensions, GSD, projection, number of candidate matches, inlier
count, and any relevant metric output.

Never include private credentials or other sensitive information in a
report.

## Feature Requests

Explain the problem being solved, the use case, why current behavior is
insufficient, a proposed approach if you have one, alternatives considered,
and any expected architectural or benchmark impact.

Prefer "Evaluate model X because V2 currently fails under this documented
modality-stress case" over "Add model X because it's popular."

## Research Proposals

For larger research ideas, describe: the research question, motivation,
baseline, proposed method, datasets, metrics, expected experiment,
computational requirements, and integration risk. Examples of relevant
topics include IIRS spectral-to-structural representations,
illumination-invariant descriptors, DEM-aware registration, lunar-specific
learned features, uncertainty estimation, and piecewise geometric models.

## Pull Requests

### Before Opening a PR

1. Sync with the current default branch.
2. Confirm your branch is focused on a single purpose.
3. Add or update tests for your change.
4. Run the relevant checks locally.
5. Update documentation affected by the change.
6. Update `CHANGELOG.md` if the change is notable (see below).
7. Verify no secrets or large accidental files are included.
8. Review your own diff.

### PR Description

Explain: what changed and why, how it was tested, affected modules,
benchmark impact (if any), screenshots/visuals where relevant, known
limitations, and follow-up work if applicable.

### PR Size

Keep pull requests reasonably focused. Avoid combining, for example, a large
refactor, a new matcher, a UI redesign, and a dependency overhaul into one
unrelated mega-PR. Discuss large architectural changes before implementing
them.

### Draft Pull Requests

Use draft PRs for early architecture feedback, research prototypes,
work-in-progress features, or large refactors. A draft is not merge-ready by
default.

### PR Checklist

- [ ] My change has a clear scope.
- [ ] I followed existing project conventions.
- [ ] I added or updated tests where appropriate.
- [ ] Relevant tests pass locally.
- [ ] I updated documentation where required.
- [ ] I did not commit credentials or secrets.
- [ ] I did not accidentally commit large datasets or generated artifacts.
- [ ] I documented new dependencies.
- [ ] I documented benchmark methodology changes.
- [ ] I compared research changes against an appropriate baseline.
- [ ] I included failure cases or limitations where relevant.
- [ ] I updated `CHANGELOG.md` if the change is notable.
- [ ] I verified terminology and sensor claims.
- [ ] I reviewed my own diff.

### Review Process

Reviewers may evaluate correctness, architecture fit, maintainability, test
coverage, reproducibility, scientific validity, documentation, performance,
security, dependency impact, and benchmark integrity. Review comments should
focus on the contribution, not the contributor.

Maintainers may use whatever merge strategy is configured for the
repository on GitHub. This guide does not promise a specific review-count
requirement, approval process, or response-time SLA beyond what the
repository explicitly configures.

## Breaking Changes

Clearly identify incompatible changes — for example, API schema changes,
CLI renaming, configuration changes, output format changes, benchmark
definition changes, dataset manifest changes, or index incompatibility.

In the PR description, use a **Breaking Change** callout that explains what
changed, who is affected, and the migration path.

## Changelog

Update `CHANGELOG.md` for notable changes: user-facing features, significant
fixes, breaking changes, benchmark methodology changes, supported-sensor
changes, major evaluation changes, API changes, and security fixes. Small
internal changes don't need a changelog entry. Remember the distinction:
`CHANGELOG.md` records what changed; `ROADMAP.md` records planned future
direction.

## Security

Do not report security vulnerabilities through a public issue. Follow the
private reporting process described in [`SECURITY.md`](SECURITY.md).

## Licensing and Attribution

By contributing, you agree your contribution is submitted under the
repository's existing license as defined in [`LICENSE`](LICENSE). Do not
submit code or data you do not have the right to redistribute.

Do not copy code from other repositories without checking its license,
attribution requirements, and compatibility. If an implementation materially
derives from a research publication (for example, methods related to
LightGlue, LoFTR, ALIKED, RIFT, or CFOG), document the citation and preserve
any required license notices or original attribution.

## AI-Assisted Contributions

AI tools may be used as development aids, but contributors remain fully
responsible for everything they submit. Before submitting AI-assisted work:

- Review and test all generated code.
- Verify APIs, dependencies, and citations the AI referenced.
- Verify any scientific claims independently.
- Remove hallucinated functionality.
- Ensure no secrets are exposed.
- Do not submit code you do not understand.

AI-generated explanations or comments must accurately reflect the actual
implementation. Do not treat AI output as a trusted scientific source.

## What We Generally Avoid

Contributions may not be accepted if they:

- duplicate existing functionality without justification
- introduce unnecessary dependencies
- break benchmark reproducibility
- weaken existing tests
- hide failure cases
- make unsupported scientific claims
- commit unlicensed data or code
- expose secrets
- mix unrelated changes together
- add significant complexity without measurable benefit
- conflict with existing project architecture without prior discussion

## Getting Help

Use GitHub Issues, GitHub Discussions (if enabled for this repository), or
pull-request discussion threads to ask questions or get feedback.

## Contributor Attribution

Meaningful contributions are preserved through Git history and GitHub's
contribution records. If contributor metadata is maintained through
[`CITATION.cff`](CITATION.cff), changes to authorship follow the project
maintainers' policy. Contributing does not guarantee authorship on any
associated research paper.

## Final Contributor Checklist

- [ ] I understand the scope of my change.
- [ ] My branch contains only relevant changes.
- [ ] I followed the existing project architecture.
- [ ] I tested the affected functionality.
- [ ] I added tests where appropriate.
- [ ] I updated relevant documentation.
- [ ] I verified benchmark claims where applicable.
- [ ] I documented methodology changes.
- [ ] I did not commit secrets or private configuration.
- [ ] I did not accidentally commit large datasets or artifacts.
- [ ] I checked third-party licenses and attribution.
- [ ] I reviewed my own diff.
- [ ] I updated the changelog if the change is notable.

Thank you for helping build ChandraMap.
