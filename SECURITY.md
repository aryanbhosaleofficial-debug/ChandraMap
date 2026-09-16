# Security Policy

ChandraMap is an open-source research and engineering project for lunar
image correspondence and registration. This policy explains how to report a
security vulnerability, what is in and out of scope, and what security
practices are expected of contributors and users. It does not cover general
contribution workflow (see [`CONTRIBUTING.md`](CONTRIBUTING.md)) or
community behavior (see [`CODE_OF_CONDUCT.md`](CODE_OF_CONDUCT.md)).

## Supported Versions

ChandraMap is currently under active development. Until stable, versioned
software releases are published, security fixes are generally applied to
the latest supported development version rather than to a fixed set of
maintained release branches.

Note that ChandraMap's benchmark/research configurations (Benchmark V1
through Benchmark V4) are experimental pipeline configurations, not
software release versions, and are not tracked as separately "supported"
versions for security purposes. Security support refers to the software
itself — its codebase, dependencies, and services — not to a particular
benchmark configuration.

<!--
Maintainer note:
Once versioned releases exist, replace this section with a supported-versions
table, e.g.:

| Version | Supported |
| ------- | --------- |
| x.y.z   | ✅        |
| < x.y.z | ❌        |
-->

## Reporting a Vulnerability

### Private Reporting

Please report suspected security vulnerabilities privately rather than
through a public GitHub Issue or Discussion.

1. If GitHub Private Vulnerability Reporting is enabled for this
   repository, please use it (via the repository's "Security" tab).
2. If the maintainers have published a dedicated private security contact,
   use that channel instead.
3. If neither is currently available, please avoid publishing exploit
   details, and reach out to a repository maintainer through whatever
   non-public channel is reasonably available, rather than opening a public
   issue describing the vulnerability.

<!--
Maintainer note:
Enable GitHub Private Vulnerability Reporting or add a dedicated private
security contact here before wider public release, and update this section
accordingly.
-->

### What to Include

A useful report generally includes:

- A concise description of the vulnerability.
- The affected component, and version or commit if known.
- Environment details and any relevant prerequisites.
- Steps to reproduce.
- Expected behavior versus actual (vulnerable) behavior.
- The security impact (what an attacker could achieve).
- A proof of concept, where it can be shared safely.
- Relevant logs or stack traces.
- A suggested remediation, if you have one — though this is not required.

For file-processing issues, it helps to include the file format, the
affected parser/component, file size, relevant metadata, and a minimal
sample input where it is safe to share. For backend/API issues, include the
affected endpoint, request type, authentication state, and relevant
response.

You do not need a working patch to submit a valid report.

### What Not to Publish

Please do not post the following in a public issue, pull request, or
discussion:

- Working exploit code or weaponized payloads.
- API keys, access tokens, passwords, or other credentials.
- Personal information about yourself or others.
- Cloud secrets or production connection strings.
- Confidential infrastructure details.
- Malicious files capable of harming other users or systems.

A public GitHub Issue is not the right venue for an undisclosed, exploitable
vulnerability.

## Vulnerability Scope

### In Scope

Security issues relevant to ChandraMap may include, where the relevant
component exists in the codebase:

**Authentication / Authorization** — authentication bypass, authorization
bypass, privilege escalation, insecure access control.

**File Handling** — path traversal, arbitrary file read/write, unsafe
archive extraction, malicious upload handling, unsafe temporary-file
behavior, filename injection, uncontrolled file overwrite, or code execution
triggered through file processing.

**Backend / API** — injection vulnerabilities, server-side request forgery,
insecure deserialization, unsafe command execution, input-validation issues
with security impact, sensitive information exposure, or
authentication/session vulnerabilities.

**Dependency / Supply Chain** — a vulnerable dependency with exploitable
impact, dependency confusion, malicious package substitution, a compromised
build dependency, unsafe model-artifact loading, or a compromised container
image.

**Secrets** — committed credentials, exposed tokens, API keys, cloud
credentials, or private certificates.

**Web Security** (where a web interface exists) — XSS, CSRF where
applicable, insecure CORS configuration, authentication flaws, unsafe file
upload, or data exposure.

**ML / Model Artifact Security** — arbitrary code execution from untrusted
serialized model files, tampered checkpoints, unsafe pickle loading,
unverified model downloads, malicious model artifacts, or model
supply-chain compromise.

**CI/CD** — workflow command injection, unsafe pull-request workflow
behavior, secret exposure, untrusted code gaining privileged CI access, or
artifact poisoning.

**Container / Deployment** — privileged containers without justification,
exposed administrative services, insecure default configuration, embedded
credentials, or dangerous host mounts.

A denial-of-service concern is generally in scope when it is reliably
exploitable against a live service or a meaningful resource boundary, rather
than a generic performance limitation.

### Usually Not Security Issues

The following typically belong in the normal issue-reporting process (see
[`CONTRIBUTING.md`](CONTRIBUTING.md)) rather than the private security
process, since they are correctness or research concerns rather than
security vulnerabilities:

- Poor registration accuracy, incorrect crater matches, or low inlier
  ratio.
- RMSE regressions or other benchmark/metric discrepancies.
- Broken visualization or a failed benchmark run.
- Slow inference or general performance issues without a security
  boundary being crossed.
- Incorrect image scaling, unsupported sensor formats, or numerical
  instability without a security impact.
- Incorrect lunar coordinates or other scientific correctness bugs.

The following are also generally out of scope for this policy:

- Scientific or benchmark-methodology disagreements.
- Documentation mistakes without security impact.
- Feature requests.
- Issues that require unrealistic physical access to a maintainer's
  equipment.
- Vulnerabilities that exist exclusively in an unsupported, third-party
  deployment of ChandraMap.
- Social engineering directed at maintainers or contributors.
- Denial-of-service testing, load testing, or automated scanning that risks
  disrupting real infrastructure.

This policy governs ChandraMap's own code and the infrastructure the
project actually controls. It does not authorize testing against GitHub,
package registries, cloud providers, or scientific data infrastructure
operated by NASA, ISRO/PRADAN, LROC, or other third parties. A vulnerability
in a third-party service should generally be reported to that provider
directly, unless it is ChandraMap's own integration with that service that
creates the vulnerability — in which case the integration itself is in
scope here.

## Responsible Security Research

If you are investigating a potential vulnerability in ChandraMap, please:

- Act in good faith and avoid harm to the project, its users, or third
  parties.
- Avoid accessing more data than necessary to demonstrate the issue.
- Minimize privacy impact and avoid unnecessary data modification.
- Avoid destructive actions, persistence mechanisms, or spreading malware.
- Stop testing and report immediately if you encounter significant
  unintended impact.
- Report the vulnerability privately, and give maintainers reasonable time
  to investigate and respond before any public disclosure.

We ask security researchers to act in good faith, avoid harm, and stay
within systems actually controlled by the project.

## Disclosure and Remediation Process

Reports generally move through a process similar to:

```
Report Received
       ↓
Initial Review
       ↓
Triage / Reproduction
       ↓
Severity Assessment
       ↓
Fix Development
       ↓
Testing
       ↓
Release / Mitigation
       ↓
Coordinated Disclosure
       ↓
Optional Credit
```

As this is an actively developed open-source research project without
dedicated incident-response staffing, the maintainer will aim to
acknowledge, investigate, and coordinate a fix as time and resources allow.
Timing depends on severity, complexity, and maintainer availability — no
fixed response time or fix deadline is guaranteed.

Where practical, maintainers will try to let reporters know whether a report
has been received, whether it can be reproduced, whether more information
is needed, and when a fix or disclosure is expected — without promising
constant status updates.

Severity is assessed based on factors such as exploitability, impact,
affected users or systems, and effects on confidentiality, integrity, and
availability. ChandraMap does not assign CVSS scores or promise CVE
assignment for reported issues, though CVE assignment may be considered for
qualifying public releases where appropriate.

## Security Updates

Depending on the issue, a fix may take the form of a patched release, a
dependency update, a configuration mitigation, a documentation advisory, a
GitHub Security Advisory, a release note, or a changelog entry. Not all of
these mechanisms currently exist for this project; they are listed as
possible ways a fix may be communicated as the project matures.

Notable security fixes should eventually be reflected in
[`CHANGELOG.md`](CHANGELOG.md) once coordinated disclosure is complete or
otherwise appropriate, described at a level that avoids reproducing
weaponized exploit details — for example:

> Fixed unsafe archive extraction that could allow files to escape the
> intended destination directory.

## Secrets and Credentials

Never commit secrets to the repository, including `.env` files, API keys,
passwords, access tokens, GitHub tokens, cloud credentials, database
credentials, SSH private keys, private certificates, or signing keys. Use
`.env.example` to document required environment variable names without real
values.

**If a secret is accidentally committed, removing it from the latest commit
is not sufficient.** Once exposed, a credential should be treated as
compromised:

1. Revoke or rotate it immediately.
2. Replace it with a new credential.
3. Remove the old value from active use.
4. Clean the exposed value from repository history where appropriate.
5. Review related logs or usage for signs of misuse, where practical.

Locally, contributors should keep `.env` files out of version control,
avoid logging secrets, avoid screenshots or notebook cells containing
credentials, avoid pasting secrets into Issues or PRs, and avoid
hard-coding access tokens in source.

## Dependency and Supply-Chain Security

ChandraMap may depend on Python packages, Node packages, computer-vision and
geospatial libraries, AI/ML frameworks, GPU tooling, and native/system
packages. Contributors should add dependencies intentionally, prefer
actively maintained packages, verify package identity before adding it,
review license compatibility, watch for typo-squatted package names, avoid
unnecessary dependencies, pin or lock versions according to the project's
dependency strategy, review known security advisories, and avoid arbitrary
install-time scripts when they aren't needed.

Avoid installing dependencies from random file-sharing sites, unverified
repositories, unofficial model mirrors, or unknown binary releases; prefer
official or clearly documented sources.

The project may use automated security tooling where practical — for
example, dependency vulnerability scanning, secret scanning, static
analysis, container scanning, or GitHub's built-in security features
(such as Dependabot, dependency review, secret scanning, code scanning, or
private vulnerability reporting). None of these should be assumed enabled
unless confirmed in the repository's actual configuration.

## File, Dataset, and Model Safety

Scientific imagery and model files are inputs that should not automatically
be treated as trusted, even when they look like ordinary research data.

**Datasets and scientific files.** ChandraMap may work with formats such as
GeoTIFF, TIFF, PNG, JPEG, PDS products, hyperspectral cubes, metadata files,
archives, NumPy arrays, HDF-style products, and other scientific rasters.
Potential risks include malformed metadata, parser vulnerabilities,
oversized files, decompression bombs, archive-path traversal, malicious
filenames, memory or disk exhaustion, native-library crashes, and unsafe
external references embedded in metadata. Image dimensions and decompressed
size can be far larger than the file on disk — especially for large
GeoTIFFs, scientific rasters, hyperspectral cubes, or tiled lunar mosaics —
so resource use should be validated before allocating memory for
decompressed data.

**Archives.** Archive contents should never be extracted using paths
supplied directly by the archive. Extraction logic should guard against
`../` path traversal, absolute-path extraction, overwriting sensitive
files, and symlink abuse where relevant.

**Paths.** Backend and CLI code that accepts file paths should not trust
user-provided paths directly; consider canonicalization, restricting
operations to allowed storage roots, using generated identifiers, and
explicit output locations.

**Model and checkpoint files.** Model checkpoints are not passive data.
Risks include unsafe Python pickle serialization, malicious model loaders,
arbitrary code execution during loading, tampered checkpoints, poisoned
model artifacts, and compromised download locations. Prefer safer
serialization formats where practical, official model sources, documented
checksums, and versioned model references, and avoid loading untrusted
`.pkl`, `.pt`, `.pth`, or similar files without understanding how they are
deserialized. Not every file with these extensions is malicious, but
provenance should be verified for anything loaded from outside the
project's own controlled sources — document model name, version, upstream
repository, license, checkpoint source, and checksum where practical.

**Deserialization and configuration.** Avoid unsafe deserialization of
untrusted input, including pickle, joblib backed by pickle, arbitrary
Python objects, or unsafe YAML loading. Prefer safe YAML loading methods for
untrusted or externally supplied files, and treat configuration files as
data, not trusted code.

**File uploads (future services).** If ChandraMap exposes file upload
through a web or API service, that implementation should consider extension
and content validation, file-size limits, generated (non-user-controlled)
storage filenames, storage isolation, path normalization, archive safety,
quota controls, and temporary-file cleanup. These are security expectations
for such functionality, not a claim that upload handling currently exists
or is currently hardened.

**Subprocess execution.** Where external processes are invoked, avoid
constructing shell commands from untrusted input, prefer argument arrays
over shell strings, validate user-controlled values, avoid `shell=True`
unless carefully controlled, and avoid exposing secrets via command-line
arguments where avoidable.

**Vector indexes (e.g. FAISS).** Vector index files should not be assumed
inherently safe simply because they are numerical data. If an index can be
loaded from an external source, treat it as an untrusted artifact and
verify its provenance before loading.

**Remote fetching.** If future functionality fetches imagery from
user-supplied URLs, that functionality should restrict allowed schemes,
validate destinations, block internal/private network destinations where
appropriate, use allowlists where feasible, and enforce timeouts and size
limits, to reduce server-side request forgery risk.

**Notebooks.** Before committing a Jupyter notebook, inspect and clean
output cells, and remove any embedded secrets, filesystem paths,
environment variables, or private data.

**Research and experimental code.** Code in `research/` or `experiments/`
can be less polished than production code, but should still avoid embedded
credentials, arbitrary code execution from untrusted configuration,
uncontrolled downloads, unsafe deserialization, or destructive filesystem
behavior.

## Security Expectations for Contributors

Contributors should:

- Never commit secrets.
- Validate untrusted inputs, especially file paths, uploaded files, and
  externally supplied configuration.
- Follow safe file-handling practices as described above.
- Document any new external service integrations.
- Justify security-sensitive dependencies in their pull request.
- Add regression tests for security fixes where practical — for example,
  tests that reject unsafe paths, malformed inputs, archive traversal
  attempts, oversized files, or invalid schemas — without needing to
  publish exploit code publicly when that would create unnecessary risk.
- Avoid disabling security checks merely to make a test pass.
- Report discovered vulnerabilities privately rather than in a public PR
  or issue.
- Help keep this file current if the project's security-relevant
  architecture changes materially.

Security-sensitive changes deserve extra review attention: authentication,
authorization, file upload, path handling, subprocess execution, dependency
installation, deserialization, secrets management, network fetching,
deployment configuration, GitHub Actions workflows, model loading, and
archive extraction. Pull requests touching these areas should call out
their security implications in the PR description.

For CI/CD specifically, general principles include using least-privilege
workflow permissions, avoiding exposure of secrets to untrusted pull
requests, carefully reviewing or pinning third-party Actions (for example,
to an immutable commit SHA where higher assurance is warranted), avoiding
execution of PR-controlled scripts with privileged secrets, and protecting
publishing/release credentials. For containerized deployments, general
principles include using minimal base images, avoiding embedded secrets,
avoiding unnecessary root privileges, minimizing exposed ports, reviewing
image sources, and using `.dockerignore` to avoid leaking local files into
images. None of these should be read as a claim that a particular practice
is already implemented in this repository.

## Deployment Considerations

Anyone deploying ChandraMap — or a future backend/API built on it — in an
exposed or production environment is responsible for independently
reviewing and hardening that deployment for their context: secure defaults
(debug mode off, no default passwords, no unintentionally public
administrative interfaces), explicit upload and request-size limits,
appropriate authentication and CORS configuration where data sensitivity
requires it, resource/concurrency controls for CPU/GPU-intensive
computer-vision or AI/ML jobs, and clear separation between environments.
Logs should never contain passwords, tokens, secret keys, full credentials,
sensitive headers, or unnecessary personal information, and should avoid
dumping entire uploaded files or large scientific arrays.

Note also the distinction between security and scientific correctness:
inaccurate registration, incorrect RMSE, or incorrect lunar coordinates are
correctness/research issues and should go through the normal bug-reporting
process in `CONTRIBUTING.md`. A malicious file triggering arbitrary code
execution, or unauthorized disclosure of another user's uploaded imagery,
are security issues and should go through this policy's private reporting
process instead.

## Limitations

ChandraMap is an actively developed, largely personal/research open-source
project. It should not be assumed to have undergone formal security
certification, professional penetration testing, or regulatory compliance
review, and it does not currently offer enterprise-grade incident response
or continuous monitoring. ChandraMap does not currently operate a formal
bug bounty program.

## Related Policies

- [Contributing Guidelines](CONTRIBUTING.md) — development workflow and
  general (non-security) bug reports.
- [Code of Conduct](CODE_OF_CONDUCT.md) — community behavior and
  misconduct reporting.
- [Changelog](CHANGELOG.md) — records of completed changes, including
  security fixes once appropriate to disclose.
- [License](LICENSE) — legal terms governing use of this software.

## Acknowledgements

Reporters who responsibly disclose a valid vulnerability may be credited in
project documentation if they would like public credit, disclosure is
appropriate, and the maintainers agree. ChandraMap does not currently offer
financial rewards for vulnerability reports.
