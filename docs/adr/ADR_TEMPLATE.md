# ADR-XXXX: <Decision Title>

<!--
Architecture Decision Record template for ChandraMap.

Instructions:
- Replace `XXXX` with the assigned four-digit ADR number.
- Replace `<Decision Title>` with a concise, specific title describing the architectural decision.
- Replace all applicable placeholders before finalizing the ADR.
- Remove instructional comments that no longer add value.
- Delete optional or conditionally required sections that genuinely do not apply.
- Do not invent evidence, benchmark results, issue IDs, PR IDs, implementation status, or scientific claims.
- Preserve historical ADRs. If an accepted decision changes materially, create a new ADR and supersede the old one rather than rewriting architectural history.
-->

> **ADR principle:** Record the decision, the alternatives considered, the evidence available at the time, the trade-offs accepted, and the consequences future maintainers need to understand.

## Metadata

<!-- REQUIRED -->

| Field                   | Value                                                                                                                           |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| **Status**              | Proposed                                                                                                                        |
| **Date**                | `<YYYY-MM-DD>`                                                                                                                  |
| **Decision Owners**     | `<maintainer, contributor, role, or N/A>`                                                                                       |
| **Decision Scope**      | `<shared architecture / V1 / later version / research / backend / API / frontend / data / evaluation / infrastructure / other>` |
| **Sensor Scope**        | `<OHRC / TMC-2 / IIRS / LRO NAC / LRO WAC / all applicable sensors / reference-side only / sensor-independent / N/A>`           |
| **Supersedes**          | `<ADR-XXXX or N/A>`                                                                                                             |
| **Superseded By**       | `<ADR-XXXX or N/A>`                                                                                                             |
| **Related Issues**      | `<issue links/IDs or N/A>`                                                                                                      |
| **Related PRs**         | `<PR links/IDs or N/A>`                                                                                                         |
| **Related Experiments** | `<experiment links/IDs or N/A>`                                                                                                 |
| **Related Benchmarks**  | `<benchmark links/IDs or N/A>`                                                                                                  |

<!--
Supported ADR statuses:
- Proposed
- Accepted
- Rejected
- Deprecated
- Superseded

Do not mark an ADR Accepted merely because the proposed implementation exists.
Use the status that reflects the repository's actual decision state.
-->

---

## Context

<!-- REQUIRED

Describe the facts and constraints that created the need for this decision before defending a preferred solution.

Include only relevant context, such as:
- current system behavior;
- architectural background;
- why this decision is needed now;
- affected pipeline stage(s);
- relevant sensor characteristics;
- relevant image/product characteristics;
- existing architectural constraints;
- previous ADRs that influence the problem;
- known limitations;
- research or benchmark context;
- current implementation state where verified.

For ChandraMap scientific decisions, distinguish physical facts from implementation assumptions.

Examples of relevant considerations may include:
- OHRC, TMC-2, and IIRS are physically different sensor products;
- IIRS may require a defined 2D registration representation;
- physical GSD differs from array dimensions;
- upsampling does not create missing terrain information;
- retrieval and local registration are separate responsibilities;
- candidate matcher output is not geometrically verified truth;
- independent evaluation may be required for scientific claims.

Do not include these points mechanically when they are irrelevant to the ADR.
-->

<Describe the architectural and scientific context that led to this decision.>

---

## Problem Statement

<!-- REQUIRED

State the architectural problem concisely.

Answer:
- What specifically needs to be decided?
- Why is current behavior insufficient?
- What engineering or scientific risk exists if nothing changes?
- What is explicitly outside this decision?

Avoid turning this section into a proposed solution.
-->

<Describe the problem this ADR must resolve.>

---

## Decision Drivers

<!-- REQUIRED

List only the factors that materially influence this decision.

Possible ChandraMap decision drivers include:
- registration accuracy;
- correspondence reliability;
- scientific validity;
- benchmark comparability;
- reproducibility;
- sensor compatibility;
- physical-scale correctness;
- geospatial correctness;
- failure detectability;
- runtime;
- memory;
- scalability;
- implementation complexity;
- maintainability;
- explainability;
- dependency stability;
- licensing;
- operational simplicity.

Delete irrelevant examples. Add decision-specific drivers when needed.
-->

- `<Decision driver>`
- `<Decision driver>`
- `<Decision driver>`

---

## Scope

<!-- REQUIRED -->

### In Scope

- `<Responsibility, component, algorithmic stage, or contract covered by this decision>`
- `<Additional in-scope item>`

### Out of Scope

<!--
State adjacent concerns intentionally excluded so this ADR does not silently expand in scope.
-->

- `<Excluded responsibility or future concern>`
- `<Additional exclusion>`

### Affected Components

<!--
Examples may include:
- metadata handling;
- sensor routing;
- preprocessing;
- scale preparation;
- retrieval;
- local correspondence;
- geometric verification;
- transform estimation;
- sub-pixel refinement;
- registration;
- evaluation;
- backend/API;
- frontend;
- benchmark tooling;
- experiment infrastructure.

List only what is actually affected.
-->

- `<Component>`
- `<Component>`

### Sensor Scope

<!-- CONDITIONALLY REQUIRED for sensor-dependent decisions.

Be explicit when the decision is:
- OHRC-specific;
- TMC-2-specific;
- IIRS-specific;
- LRO reference-specific;
- common to all sensors;
- independent of sensor processing.
-->

`<Sensor applicability>`

### Version Scope

<!-- REQUIRED for decisions that may affect scientific-version behavior.

Examples:
- V1 only;
- shared across scientific versions;
- later experimental version only;
- benchmark infrastructure shared by all versions.

Do not invent V2/V3/V4 behavior.
-->

`<Scientific-version applicability>`

---

## Constraints and Assumptions

<!-- REQUIRED

Separate verified constraints from assumptions.

Potential areas:
- available metadata;
- product type or processing level;
- GSD information;
- map projection;
- source/reference overlap;
- hardware;
- memory;
- runtime;
- offline/online operation;
- dependency availability;
- dataset availability;
- ground-truth availability;
- benchmark limitations;
- licensing constraints.

Do not present an assumption as a verified fact.
-->

### Constraints

- `<Verified constraint>`
- `<Verified constraint>`

### Assumptions

- `<Assumption>`
- `<Assumption>`

### Uncertainties

<!-- OPTIONAL but recommended when important information is unresolved. -->

- `<Uncertainty or currently unknown fact>`
- `<How this uncertainty affects the decision>`

---

## Considered Options

<!-- REQUIRED

Consider at least two realistic options whenever meaningful.

Options should be viable alternatives, not one serious option surrounded by intentionally poor straw-man choices.

Delete Option C when only two meaningful choices exist.
-->

### Option A — `<Name>`

**Summary**

<Describe the approach.>

**Advantages**

- `<Advantage>`
- `<Advantage>`

**Disadvantages / Trade-offs**

- `<Trade-off>`
- `<Trade-off>`

**Evidence**

<!--
Reference actual evidence where available:
- experiment;
- benchmark;
- prototype;
- issue;
- paper;
- official documentation.

Use N/A or "Not yet evaluated" instead of inventing evidence.
-->

- `<Evidence, reference, or N/A>`

### Option B — `<Name>`

**Summary**

<Describe the approach.>

**Advantages**

- `<Advantage>`
- `<Advantage>`

**Disadvantages / Trade-offs**

- `<Trade-off>`
- `<Trade-off>`

**Evidence**

- `<Evidence, reference, or N/A>`

### Option C — `<Name>`

<!-- OPTIONAL — delete this section when there is no meaningful third option. -->

**Summary**

<Describe the approach.>

**Advantages**

- `<Advantage>`
- `<Advantage>`

**Disadvantages / Trade-offs**

- `<Trade-off>`
- `<Trade-off>`

**Evidence**

- `<Evidence, reference, or N/A>`

---

## Option Comparison

<!-- REQUIRED when multiple options require structured comparison.

Remove irrelevant criteria and add decision-specific criteria.

Prefer factual observations such as:
- "requires GPU dependency";
- "preserves current V1 behavior";
- "not yet benchmarked on IIRS";
- "adds an offline index-build stage".

Avoid arbitrary scores such as 9/10 unless the repository defines an objective scoring method.
-->

| Criterion          | Option A | Option B | Option C |
| ------------------ | -------- | -------- | -------- |
| Accuracy / quality | `<...>`  | `<...>`  | `<...>`  |
| Robustness         | `<...>`  | `<...>`  | `<...>`  |
| Runtime            | `<...>`  | `<...>`  | `<...>`  |
| Complexity         | `<...>`  | `<...>`  | `<...>`  |
| Reproducibility    | `<...>`  | `<...>`  | `<...>`  |
| Sensor coverage    | `<...>`  | `<...>`  | `<...>`  |
| Benchmark impact   | `<...>`  | `<...>`  | `<...>`  |

---

## Decision

<!-- REQUIRED

State exactly what is being adopted.

The decision should be precise enough that two engineers reading this ADR would implement the same architectural intent.

When relevant, distinguish:
- default path;
- optional path;
- experimental path;
- fallback path.

Do not describe an experimental option as the default unless the decision actually accepts it.
-->

<State the selected decision clearly and specifically.>

### Decision Boundaries

<!-- OPTIONAL but useful for complex decisions. -->

- **Default path:** `<chosen default or N/A>`
- **Optional path:** `<optional behavior or N/A>`
- **Experimental path:** `<experimental behavior or N/A>`
- **Fallback path:** `<fallback behavior or N/A>`

---

## Rationale

<!-- REQUIRED

Explain why the selected option best satisfies the decision drivers.

The rationale should connect evidence and constraints to the decision.

Weak rationale:
- "it is newer";
- "it uses AI";
- "it is popular";
- "it produced more matches".

Stronger rationale:
- controlled benchmark evidence;
- reproducibility;
- physically correct scale handling;
- sensor compatibility;
- lower complexity for equivalent measured performance;
- better independent registration accuracy;
- clearer failure semantics.

For algorithmic ADRs, prefer controlled comparisons rather than intuition alone.
-->

<Explain why this option was selected over the alternatives.>

---

## Architectural Impact

<!-- CONDITIONALLY REQUIRED when architecture, pipeline structure, ownership, or data flow changes.

Delete "Before" / "After" if the decision is easier to explain another way.
-->

### Before

```text
<Current architecture or flow>
```

### After

```text
<Architecture or flow after this decision>
```

### Responsibility Changes

| Responsibility     | Before                     | After                  |
| ------------------ | -------------------------- | ---------------------- |
| `<Responsibility>` | `<Current owner/behavior>` | `<New owner/behavior>` |
| `<Responsibility>` | `<Current owner/behavior>` | `<New owner/behavior>` |

### Dependency Direction

<!--
Describe new, removed, or intentionally preserved dependency relationships.

For example, avoid moving reusable scientific behavior into API or frontend layers unless that is deliberately justified.
-->

<Describe dependency-direction impact or state N/A.>

---

## Scientific and Algorithmic Impact

<!-- CONDITIONALLY REQUIRED when the decision can change scientific outputs, methodology, evaluation, or benchmark interpretation.

Delete this section for decisions with no scientific impact.

Explicitly identify whether the decision changes any of:
- sensor preprocessing;
- physical-scale handling;
- retrieval;
- feature extraction;
- candidate matching;
- geometric verification;
- transformation model;
- sub-pixel refinement;
- registration;
- metrics;
- failure semantics.

Important ChandraMap invariants to review when relevant:
- candidate matches != verified inliers;
- retrieval != registration;
- fit residual != independent check error;
- reference imagery != independent ground truth automatically;
- spatial coverage != accuracy;
- upsampling != recovered physical detail;
- refine verified points, then refit the final transform;
- ground error in metres is meaningful only with appropriate scale/projection/truth.
-->

**Scientific behavior change:** `<yes / no / experimental / unknown>`

<Describe the scientific impact.>

### Pipeline Ordering

<!-- OPTIONAL. Use when stage order is part of the decision. -->

```text
<Relevant scientific stage order>
```

### Coordinate / Unit Semantics

<!-- CONDITIONALLY REQUIRED when coordinates, scale, transformations, or metrics are affected. -->

- **Input coordinate space:** `<...>`
- **Output coordinate space:** `<...>`
- **Transform direction:** `<...>`
- **Primary error unit:** `<...>`
- **Ground-unit conversion:** `<conditions under which valid, or N/A>`

---

## Sensor Impact

<!-- CONDITIONALLY REQUIRED for sensor-aware architectural decisions.

Do not assume identical behavior across OHRC, TMC-2, and IIRS.

For a specific scientific product, product metadata takes precedence over generic approximate sensor summaries.
-->

| Sensor / Reference | Impact                          | Notes                                                |
| ------------------ | ------------------------------- | ---------------------------------------------------- |
| OHRC               | `<affected / unaffected / N/A>` | `<...>`                                              |
| TMC-2              | `<affected / unaffected / N/A>` | `<...>`                                              |
| IIRS               | `<affected / unaffected / N/A>` | `<representation/modality implications if relevant>` |
| LRO NAC            | `<affected / unaffected / N/A>` | `<...>`                                              |
| LRO WAC            | `<affected / unaffected / N/A>` | `<...>`                                              |

---

## Scientific Version Impact

<!-- CONDITIONALLY REQUIRED when a scientific version is affected.

ChandraMap scientific versions are benchmarkable research milestones, not ordinary software release numbers.

Do not silently change V1 baseline behavior.
If this decision intentionally changes an accepted baseline, explain whether a new scientific version or superseding ADR is required.
-->

- **Affected scientific version(s):** `<...>`
- **Baseline behavior changed:** `<yes / no>`
- **Historical comparability affected:** `<yes / no / unknown>`
- **New version boundary required:** `<yes / no / under review>`

<Explain version impact.>

---

## Benchmark and Evaluation Impact

<!-- CONDITIONALLY REQUIRED for algorithmic, scientific, metric, retrieval, or performance decisions.

Distinguish:
- software tests;
- benchmark evidence;
- retrieval metrics;
- local-registration metrics;
- fit residuals;
- independent check-point error.

Do not invent results.
-->

### Benchmark Contract

- **Benchmark definition changed:** `<yes / no>`
- **Pair population changed:** `<yes / no>`
- **Ground truth / check data changed:** `<yes / no>`
- **Metric definition changed:** `<yes / no>`
- **Failure policy changed:** `<yes / no>`
- **Configuration policy changed:** `<yes / no>`

### Metrics Affected

<!-- Delete irrelevant metrics. -->

- `<Recall@K if retrieval is affected>`
- `<candidate match count>`
- `<verified inlier count / ratio>`
- `<spatial coverage>`
- `<reprojection residual>`
- `<independent check-point RMSE>`
- `<source-image pixel error>`
- `<ground error when scientifically valid>`
- `<runtime>`
- `<memory>`
- `<success/failure rate>`

### Evidence Available at Decision Time

| Evidence                               | Result / Reference                         | Interpretation                  |
| -------------------------------------- | ------------------------------------------ | ------------------------------- |
| `<Experiment / benchmark / prototype>` | `<link, ID, result, or not yet available>` | `<What this evidence supports>` |
| `<Additional evidence>`                | `<...>`                                    | `<...>`                         |

<!--
If no controlled evidence exists, say so explicitly.
An ADR may still record a necessary architectural decision, but uncertainty should remain visible.
-->

---

## Validation Plan

<!-- REQUIRED

Describe how the team will determine whether the decision works as intended.

Validation can include:
- unit/component tests;
- integration tests;
- contract tests;
- regression tests;
- controlled scientific experiments;
- benchmark comparisons;
- stress tests;
- performance measurements;
- failure-case evaluation.

Do not use screenshots alone as scientific validation.
-->

### Software Validation

- [ ] `<Validation step>`
- [ ] `<Validation step>`

### Scientific Validation

<!-- CONDITIONALLY REQUIRED for scientific/algorithmic decisions. -->

- [ ] `<Controlled experiment or benchmark comparison>`
- [ ] `<Independent evaluation requirement>`
- [ ] `<Relevant stress case>`

### Acceptance Evidence

<!--
Do not invent thresholds here unless another authoritative specification already defines them.
-->

<Describe the evidence required to confirm that the decision behaves as intended.>

---

## Consequences

<!-- REQUIRED

Record both desirable and undesirable consequences.

An ADR is incomplete if it records only benefits.
-->

### Positive Consequences

- `<Positive consequence>`
- `<Positive consequence>`

### Negative Consequences / Trade-offs

- `<Accepted cost, complexity, dependency, performance loss, or limitation>`
- `<Additional trade-off>`

### Neutral Consequences

<!-- OPTIONAL -->

- `<Behavior or responsibility that changes without being inherently positive or negative>`

---

## Implementation Impact

<!-- CONDITIONALLY REQUIRED once implementation implications are known.

Do not claim implementation is complete unless verified.
-->

### Required Changes

- `<Component or module change>`
- `<Configuration/schema change>`
- `<Test change>`
- `<Documentation change>`

### Implementation Sequence

<!-- OPTIONAL -->

1. `<Step>`
2. `<Step>`
3. `<Step>`

### Implementation Status

`<not started / partial / implemented / N/A — use only the repository's verified state>`

---

## Migration and Compatibility

<!-- CONDITIONALLY REQUIRED when existing code, data, APIs, configurations, benchmarks, results, or versions may be affected. -->

### Compatibility Impact

- **Public/internal API:** `<impact or N/A>`
- **Configuration:** `<impact or N/A>`
- **Result schema:** `<impact or N/A>`
- **Stored artifacts:** `<impact or N/A>`
- **Benchmark history:** `<impact or N/A>`
- **Scientific reproducibility:** `<impact or N/A>`

### Migration

<Describe any required migration, transition period, data regeneration, or N/A.>

### Backward Compatibility

<Explain what remains compatible and what does not.>

---

## Data and Artifact Impact

<!-- CONDITIONALLY REQUIRED when the decision affects mission products, datasets, caches, indexes, generated representations, results, or benchmark artifacts. -->

- **New data required:** `<...>`
- **Derived data generated:** `<...>`
- **Storage implications:** `<...>`
- **Cache/index implications:** `<...>`
- **Redistribution/licensing implications:** `<...>`
- **Provenance requirements:** `<...>`

<!--
Do not commit or redistribute mission data merely because the architecture uses it.
Follow the repository's data and licensing policy.
-->

---

## Dependency and Runtime Impact

<!-- CONDITIONALLY REQUIRED when dependencies or compute requirements change. -->

### Dependencies

- **Added:** `<dependency or N/A>`
- **Removed:** `<dependency or N/A>`
- **Updated:** `<dependency or N/A>`
- **Optional / research-only dependencies:** `<...>`

### Runtime Environment

- **CPU impact:** `<...>`
- **GPU/accelerator impact:** `<...>`
- **Memory impact:** `<...>`
- **Storage impact:** `<...>`
- **Offline/online impact:** `<...>`

<!--
Do not invent measured runtime, hardware requirements, or memory values.
If not evaluated, say "Not yet measured".
-->

---

## Security and Operational Impact

<!-- CONDITIONALLY REQUIRED for backend, API, storage, external-service, upload, filesystem, or dependency decisions. -->

Consider where relevant:

- path handling;
- untrusted files;
- serialization;
- command execution;
- secret management;
- external network access;
- dependency risk;
- sensitive logs;
- service exposure.

<Describe security/operational consequences or state N/A.>

---

## Reproducibility Impact

<!-- REQUIRED for scientific decisions; OPTIONAL for unrelated infrastructure decisions.

Explain whether the decision changes what must be recorded to reproduce a run.
-->

A scientifically significant result may need to preserve:

- code revision;
- scientific version;
- resolved configuration;
- source/reference identities;
- product metadata;
- representation provenance;
- benchmark version;
- truth/check-point version;
- dependency/environment context;
- randomness context where relevant.

**New provenance requirements:** `<...>`

**Historical reproducibility impact:** `<...>`

---

## Risks and Open Questions

<!-- REQUIRED when meaningful uncertainty remains.

Do not turn unknowns into assumptions without saying so.
-->

### Risks

- `<Risk>`
- `<Risk>`

### Open Questions

- `<Question still unresolved>`
- `<Question still unresolved>`

### Known Limitations

- `<Limitation accepted by this decision>`
- `<Limitation that requires future evaluation>`

---

## Rejected Alternatives

<!-- OPTIONAL

Use this section when an alternative deserves a durable explanation beyond the Considered Options section.

Explain why the alternative was not selected without overstating its weakness.
-->

### `<Alternative>`

<Explain why it was not selected at this time.>

---

## Reversal and Supersession

<!-- REQUIRED for decisions whose reversal would be non-trivial; otherwise keep concise.

Architecture decisions are historical records.
Do not rewrite an accepted ADR merely because the architecture later changes.
Create a new ADR and update Superseded By / Supersedes metadata.
-->

**Reversibility:** `<easy / moderate / difficult / unknown, with explanation>`

**What would justify revisiting this decision?**

- `<New benchmark evidence>`
- `<Changed requirement>`
- `<Dependency/platform change>`
- `<New sensor/data constraint>`
- `<Other trigger>`

**Supersession process:** `<Describe any decision-specific requirement or use standard ADR supersession>`

---

## References and Traceability

<!-- REQUIRED when supporting material exists.

Use repository-relative links for repository documents.
Use authoritative external sources for sensor specifications, algorithms, libraries, or mission data.

Do not invent URLs, issue IDs, PR IDs, experiment IDs, or paper citations.
-->

### Repository Documentation

- `<relative path or N/A>`

### Requirements / Specifications

- `<relative path, requirement ID, or N/A>`

### Experiments

- `<experiment ID/link or N/A>`

### Benchmarks

- `<benchmark/result link or N/A>`

### Issues / Pull Requests

- `<issue/PR link or N/A>`

### External References

- `<official mission documentation, primary paper, official library documentation, or N/A>`

---

## Decision Traceability

<!-- OPTIONAL but recommended for scientifically significant ADRs. -->

```text
Requirement / Research Question
        ↓
Architecture Decision
        ↓
Implementation
        ↓
Tests
        ↓
Experiment / Benchmark
        ↓
Result
        ↓
Follow-up ADR if needed
```

---

## ADR Completion Checklist

<!--
Use this checklist before changing the ADR status from Proposed to Accepted.
Delete checklist items that genuinely do not apply rather than checking meaningless boxes.
-->

### Decision Quality

- [ ] The architectural problem is clearly stated
- [ ] The decision is specific enough to implement consistently
- [ ] Relevant alternatives were considered
- [ ] Trade-offs are explicit
- [ ] The rationale is based on actual constraints or evidence
- [ ] Uncertainty is stated rather than hidden

### Scope

- [ ] In-scope and out-of-scope boundaries are clear
- [ ] Affected components are identified
- [ ] Sensor scope is explicit where relevant
- [ ] Scientific-version scope is explicit where relevant

### Scientific Integrity

- [ ] Physical-scale assumptions are valid
- [ ] Product metadata takes precedence over generic sensor summaries
- [ ] IIRS is not treated implicitly as an ordinary grayscale image
- [ ] Candidate matches are distinguished from verified inliers
- [ ] Retrieval is distinguished from local registration
- [ ] Fit residuals are distinguished from independent evaluation
- [ ] Sub-pixel refinement ordering is correct where relevant
- [ ] Refined tie points lead to a refitted final transform where required
- [ ] Ground-distance claims are made only when scientifically meaningful
- [ ] No unsupported accuracy, robustness, or invariance claim is introduced

### Benchmarking and Reproducibility

- [ ] Benchmark impact is documented
- [ ] Baseline comparability is preserved or the break is explicit
- [ ] Evidence is referenced rather than fabricated
- [ ] Failed cases are not hidden
- [ ] Reproducibility implications are documented
- [ ] Required provenance is identified

### Engineering

- [ ] Dependency implications are understood
- [ ] Runtime/resource implications are documented where relevant
- [ ] Migration or compatibility effects are documented
- [ ] Security/data implications are considered
- [ ] Implementation responsibilities are clear

### Documentation History

- [ ] Related ADRs are referenced
- [ ] Supersession metadata is correct
- [ ] Related documentation is linked
- [ ] The ADR preserves historical context rather than rewriting an earlier accepted decision

---

<!--
Before finalizing:
1. Remove unused optional sections.
2. Replace all applicable placeholders.
3. Verify repository-relative links.
4. Verify evidence and implementation-status claims.
5. Keep unresolved evidence visibly unresolved.
6. Do not convert experimental results into broader claims than the benchmark supports.
-->

<!-- Source request specification: :contentReference[oaicite:0]{index=0} -->
