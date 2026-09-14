# Mobile Security Configuration Evaluator  (Architecture & Threat Model)

A secure architecture design and STRIDE-based threat model for an Android application that evaluates the security posture of the device it runs on.

This repository contains **design and security-analysis documentation**, not an implementation. Its purpose is to work through a complete secure-design cycle for a realistic mobile system: architectural decomposition, asset classification, trust-boundary analysis, threat identification, and control justification — evaluated against real Android platform constraints rather than an idealised model.

---

## The system being designed

The Mobile Security Configuration Evaluator (MSCE) is a hypothetical Android application that inspects security-relevant device configuration: screen lock state, developer options, patch level, installation sources, network posture, and produces a risk summary with actionable recommendations.

It is deliberately **read-only and local-only**: it changes nothing on the device, and no data leaves the handset.

This makes it an interesting design subject rather than a trivial one. An application whose entire purpose is to read sensitive security state is simultaneously a high-value target and a privacy risk in its own right. The threat model takes that tension seriously: a significant portion of the analysis concerns protecting the evaluator's own findings, because a stored history of a device's weaknesses is an attacker's reconnaissance report.

---

## Contents

| Document | What it covers |
|---|---|
| [`docs/01-architecture.md`](docs/01-architecture.md) | N-tier decomposition, security boundary analysis, platform feasibility study, data-handling design, request/response flow, quality-attribute evaluation |
| [`docs/02-threat-model.md`](docs/02-threat-model.md) | Trust levels, asset identification and criticality, system decomposition, entry/exit points, trust boundaries, STRIDE analysis, attack trees, control mapping |

---

## What distinguishes this analysis

**1. Honest security boundaries.**
Layered mobile architectures are routinely described as enforcing least privilege between tiers. On Android they do not: all tiers of a single app run in one process under one UID. This design separates what is *organisationally* useful from what is *actually enforced* — the OS sandbox and the hardware-backed Keystore — and justifies the layering on the grounds it genuinely delivers: auditability, reduced accidental exposure, and a reviewable attack surface. See [Security Boundaries](docs/01-architecture.md#6-security-boundaries-enforced-versus-logical).

**2. A platform feasibility study.**
Several checks a device-posture scanner would "obviously" perform are no longer possible on modern Android. Global unknown-sources state was removed in API 26. Installed-app enumeration became a restricted permission in API 30. Network posture requires location permission. Rather than specifying capabilities that cannot be built, the design documents each check against its API constraint and defines explicit degradation behaviour. See [Platform Feasibility](docs/01-architecture.md#7-platform-feasibility-analysis).

**3. Treating false assurance as a security risk.**
A device-security scanner that silently omits checks it could not perform, and still prints a confident score, is worse than no scanner at all. Unavailable checks are surfaced as `Unavailable`, excluded from scoring, and shown to the user with the reason. See [Scoring Integrity](docs/01-architecture.md#82-scoring-integrity-and-false-assurance).

**4. Controls mapped to a recognised standard.**
Security controls are mapped to OWASP MASVS 2.0 control groups rather than presented as an unstructured list. See [Control Mapping](docs/02-threat-model.md#10-security-controls-and-masvs-mapping).

**5. Stated limits.**
Client-side root detection is defeated by widely available tooling; the model says so and positions the control as user advisory rather than enforcement. Known weaknesses of the analysis itself are documented rather than omitted.

---

## Standards and references used

- **OWASP MASVS 2.0** — Mobile Application Security Verification Standard, control-group structure
- **OWASP Threat Modeling Process** — decomposition and trust-boundary methodology
- **Microsoft STRIDE** — threat classification
- **Android Developers platform documentation** — behaviour changes for API 26, 29, 30, 31
- **Google Play Developer Policy** — restricted permission requirements
- **NIST SP 800-57** — key management principles

Full citations in each document.

---

## Scope and status

Design documentation only. No implementation is provided in this repository. The architecture targets Android API 23 and above; the feasibility analysis reflects platform behaviour as of 2026.
