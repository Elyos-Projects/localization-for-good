# TASKS — localization-for-good

> Status: Draft · Version: 0.1.0 · Last updated: 2026-06-28 · Owner: J. Carter (acting maintainer) · Lane: donated

The backlog for the `localization-for-good` good-deed project. Read alongside
[PLAN.md](./PLAN.md). Milestones (M0–M3) match the roadmap there.

## How these tasks map to Elyos

Each task below becomes an **Elyos Task JSON** validated against
`packages/schema/src/schemas.ts` (AJV / JSON Schema draft-07). Field mapping:

- **id** — stable slug id, e.g. `localization-for-good-registry-001` (table column `ID`).
- **title** — the task title.
- **project** — always `localization-for-good`.
- **type** — one of `code | research | writing | data | design-spec | maintenance` (table `Type`).
- **lane** — `donated` for all current tasks (no funded/API execution). Funded tasks would need
  `fundedBudgetUsd`; none here.
- **priority** — `high | medium | low`.
- **domain** — array, e.g. `["localization","i18n","open-source"]`.
- **riskTier** — `low | medium | high`. Translation/glossary/review tasks are **medium** (need a
  qualified native-speaker reviewer); pure tooling/process/registry tasks are **low**; any task on
  a `riskDomain`≠`none` project (medical/legal/safety strings) is **high** (table `Risk`).
- **urgent** — boolean (default `false`; no live emergency).
- **deliverable** — `pr | dataset | document | translation` (table `Deliverable`). Registry/glossary/
  TM artifacts → `dataset`; checklists/runbooks → `document`; tooling/lint → `pr`; translated
  catalogs → `translation`; upstream submissions → `pr`.
- **tokenEstimate** — `small | medium | large` (table `Size`).
- **status** — `open | in-progress | review | delivered | done` (all start `open`).
- **context / objective** — why + what.
- **acceptanceCriteria[]** — checkable bullets (listed below tables for key tasks).
- **resources[]** — links/paths (registry, glossary, TM, source catalog URLs, templates, CLDR).
- **output** — path/description of the produced artifact.
- **requestor** — upstream maintainer; `TO BE SECURED` until a project consents.
- **verifiedNeed** — boolean; **`false`** wherever value depends on an unsecured consenting upstream.
- **outputLicense** — **MIT** for project tooling/code and project-metadata datasets (registry,
  lint); **the upstream project's own license** for translated catalogs (derivative works, e.g.
  `Apache-2.0`, `GPL-3.0-or-later`, `MPL-2.0`) — **never** a relicense.

---

## Milestone M0 — Foundation & cold-start (no upstream partner required)

Goal: build the eligibility/registry/lint/glossary/review/contribution machinery and prove the
pipeline on one project's catalog into one locale, end-to-end **except** submission/merge.

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| localization-for-good-registry-001 | Project eligibility rubric + vetted-project registry (license, format, channel, CLA/DCO, risk domain, pinned commit+hash, consent status) | data | medium | low | dataset | — | Maintainer / Compliance reviewer |
| localization-for-good-lint-001 | i18n correctness lint v1 (placeholder parity, CLDR plural completeness, markup/escaping) for ≥3 formats | code | medium | low | pr | — | Maintainer |
| localization-for-good-schema-001 | Content JSON schemas (registry/glossary/review/contribution) + CI structural check | code | small | low | pr | registry-001 | Maintainer |
| localization-for-good-review-001 | Native-speaker reviewer checklist (accuracy/fluency/register/cultural/non-partisan) | writing | small | low | document | — | Maintainer |
| localization-for-good-contrib-001 | Contribution-compliance checklist + style-guide template (CLA/DCO, channel, attribution, string-freeze timing) | writing | small | low | document | registry-001 | Compliance reviewer |
| localization-for-good-glossary-001 | Glossary + translation memory (TMX) for first language pair (≥30 terms) | data | small | medium | dataset | registry-001 | Qualified reviewer |
| localization-for-good-license-000 | **BLOCKING:** confirm eligibility + open license + channel/CLA + **maintainer consent** for first target project/locale | research | small | low | document | registry-001 | Compliance reviewer / Maintainer |
| localization-for-good-translate-001 | Translate one vetted project's UI catalog into one locale (lint-clean, reviewed; submission deferred) | translation | medium | medium | translation | registry-001, lint-001, glossary-001, review-001, **license-000** | Qualified reviewer (+2nd if safety-domain) |
| localization-for-good-watcher-001 | Source-sync watcher (hash-diff vs. pinned commit) [minimal in M0, automated in M1] | code | small | low | pr | registry-001, schema-001 | Maintainer |

**Acceptance criteria — key M0 tasks**

`registry-001` (eligibility rubric + registry)
- `projects/registry.yaml` lists **≥ 5 vetted projects**, each scored against the 4-criterion
  eligibility rubric (open license; public-good mission, not for-profit-primary; real
  language-blocked users; accepts translation contributions).
- Each entry records: name, repo, url, mission, sector, **license**, `i18nFormat`,
  `contributionChannel`, `claType`(dco|cla|none) + `claUrl`, `styleGuideUrl`, `glossaryUrl`,
  `riskDomain`(none|medical|legal|safety), **`sourceCommit` + `sourceCatalogHash`**,
  `maintainerConsent`(none|requested|granted|declined) + `consentEvidenceUrl`, `verifiedBy`,
  `verifiedDate`.
- **No-license repos and for-profit-primary projects are excluded** with `excludedReason`.
- Validates against `registrySchema` and passes the CI structural check.

`lint-001` (i18n correctness lint)
- Detects, for a translated catalog vs. its source: **missing/extra/renamed placeholders**
  (`{name}`, `%s`, `%1$s`, `<a>…</a>`), **incomplete CLDR plural categories** for the target
  locale, **unbalanced markup/tags**, **bad escaping/encoding** (non-UTF-8, broken entities).
- Supports **≥ 3 formats** (e.g. PO, JSON/ICU, Android `strings.xml`) with format-specific rules;
  has unit tests with known-bad fixtures that must fail and known-good that must pass.
- Exits non-zero on any violation so it can be a CI/pre-review hard gate. **Agent-neutral** (no
  vendor logic); lives outside `adapters/`.

`license-000` (BLOCKING prerequisite of `translate-001`)
- For the **specific project + locale** chosen for `translate-001`, confirm and record on its
  registry entry: (a) **eligible + open-licensed**; (b) **contribution channel + CLA/DCO**
  requirement known and satisfiable; (c) **maintainer consent** to receive translation
  contributions (documented evidence the project welcomes them; explicit invitation preferred).
- If any cannot be confirmed, the project is **rejected** and a different eligible project chosen.
  `translate-001` **must not start** until this passes.

`translate-001` (first catalog)
- `license-000` passed **before drafting** (eligibility + open license + channel/CLA + consent
  evidence recorded).
- One vetted project's UI catalog translated into one target locale in the **project's native
  format**, UTF-8.
- **Every placeholder/variable, ICU plural structure, and markup tag preserved exactly**; all
  required **CLDR plural categories** present; glossary/TM terminology applied; **fuzzy TM matches
  human-decided, not auto-accepted**.
- **`lint-001` green** on the deliverable (hard gate, before review).
- Agent **`UNCERTAIN:` flags** captured into `review.yaml` as `agentFlags`; **no sign-off while any
  flag is unresolved**.
- **Qualified native-speaker sign-off recorded in the PR**, reviewer **independent of drafting**
  (COI declared); checklist completed (incl. register/formality + cultural fit + non-partisan for
  civic). If `riskDomain`≠`none`: **second reviewer + back-translation + expert sign-off**,
  disclaimers verbatim.
- Ships `provenance.yaml` (project, **source commit + hash**, glossary/TM version, translator,
  reviewer) and `contribution.yaml` (channel, CLA/DCO status, attribution). Submission deferred to
  M2; `verifiedNeed: false`.

`glossary-001` (glossary + TM)
- ≥ 30 source→target entries with `register`, do-not-translate / preserve-verbatim items, and
  placeholder conventions; TMX seeded; reviewed by a qualified reviewer for the pair.

**M0 Definition of Done:** eligibility rubric + registry (≥5 vetted projects, each with license,
format, channel, CLA/DCO, risk domain, **pinned commit+hash**, consent status) + content schemas +
CI structural check + **i18n-lint (≥3 formats)** + reviewer checklist + contribution-compliance
checklist + style-guide template + one glossary/TM merged; **`license-000` confirmed (incl.
maintainer consent) before drafting**; **one catalog translated into one locale, lint-clean, with
qualified native-speaker sign-off** (submission deferred to M2). The "100% / ≥90%" metrics are
**effective from M1**; M0 verifies the first deliverable via the minimal lint + manual review.
M0 deliverables carry `verifiedNeed: false`.

---

## Milestone M1 — Repeatability & reviewer network

Goal: make the pipeline repeatable and recruit/qualify native-speaker reviewers.

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| localization-for-good-reviewers-001 | Reviewer qualification criteria + onboarding (independence, two-reviewer rule, conflict resolution) | writing | small | low | document | review-001 | Maintainer |
| localization-for-good-reviewers-002 | Recruit/engage ≥2 native-speaker reviewers or a localization-community partner | research | medium | low | document | reviewers-001 | Maintainer / Steward |
| localization-for-good-lint-002 | i18n-lint v2: ≥5 formats (add Fluent, Apple .stringsdict, XLIFF, Rails YAML) + CI enforcement | code | medium | low | pr | lint-001 | Maintainer |
| localization-for-good-contrib-002 | CLA/DCO + contribution-compliance enforcement tooling (lint registry + deliverable metadata) | code | medium | low | pr | contrib-001, schema-001 | Compliance reviewer |
| localization-for-good-glossary-002 | Extend glossary + TM to a second language pair | data | small | medium | dataset | glossary-001 | Qualified reviewer |
| localization-for-good-translate-002 | Translate a second project's catalog (different format) | translation | medium | medium | translation | translate-001, lint-002 | Qualified reviewer |
| localization-for-good-process-001 | End-to-end pipeline runbook (intake → lint → review → submit) | writing | small | low | document | translate-001, contrib-001 | Maintainer |

**Acceptance criteria — key M1 tasks**

`reviewers-001`
- Objective criteria for "qualified native-speaker reviewer" (native/near-native target language,
  localization experience, COI declaration), plus onboarding/sign-off workflow.
- Defines **reviewer independence** (drafting human ≠ sole reviewer), the **mandatory second
  reviewer + back-translation** for `riskDomain`≠`none` strings, and the **disagreement/conflict-
  resolution rule** (reconcile against source/glossary/style guide → escalate to third
  reviewer/maintainer → conservative reading wins; recorded in `review.yaml`).

`lint-002`
- Extends `lint-001` to **≥ 5 formats** (Fluent, Apple `.stringsdict`, XLIFF, Rails YAML added);
  runs in CI as a **hard gate** on every translated deliverable. This makes the "100% i18n-lint
  compliance" metric **automated from M1**.

`contrib-002`
- Validates each deliverable's `contribution.yaml` against its registry entry and **fails** if
  CLA/DCO is unsigned, channel is wrong, attribution missing, or output license incompatible with
  the upstream project; runs in CI. Makes "100% contribution-compliance" **automated from M1**.

`watcher-001` (completes in M1)
- Automated source-sync watcher re-fetches each vetted source catalog, recomputes
  `sourceCatalogHash`, and **flags drift** (changed/added source strings → fuzzy translations) for
  re-sync; runs on a schedule/CI.

**M1 Definition of Done:** qualification criteria published (incl. independence, two-reviewer rule,
back-translation gate, conflict resolution) and **≥ 2 qualified reviewers** (or a localization-
community partnership) engaged; glossary + TM cover **≥ 2 language pairs**; **i18n-lint covers ≥ 5
formats and is enforced in CI** (`lint-002`); **CLA/DCO + contribution-compliance enforced**
(`contrib-002`); **automated source-sync watcher operating** (`watcher-001`); a second project's
catalog translated + reviewed; pipeline runbook merged. **Steward named** (governance prerequisite
for M2).

---

## Milestone M2 — First upstream delivery (needs maintainer consent)

Goal: get a reviewed translation **merged and released** upstream. **All tasks gated on a
consenting upstream** (`verifiedNeed` flips to `true` only when consent is confirmed).

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| localization-for-good-partner-001 | Secure first consenting upstream; agree locale + scope + process | research | medium | low | document | reviewers-002 | Steward / Maintainer |
| localization-for-good-translate-003 | Translate agreed catalog scope into agreed locale (lint-clean, reviewed) | translation | large | medium | translation | partner-001, glossary-002 | Qualified reviewer |
| localization-for-good-submit-001 | Submit via project channel (sign CLA/DCO); shepherd to **merge + release** | code | medium | medium | pr | translate-003, contrib-002 | Steward / Maintainer |

**Acceptance criteria — key M2 tasks**

`partner-001`
- Outreach (acting maintainer → Steward) targets named candidate sources (humanitarian/civic/
  accessibility/open-education OSS maintainers; translatewiki / Mozilla L10n / Weblate libre
  community / Localization Lab), aiming for **≥ 3 serious maintainer conversations by end of M1**.
- **Pause/decision point:** if **no upstream consents by end of M1**, the maintainer makes an
  explicit **go / pivot / hold** decision before further translation work.
- A named upstream project confirmed **in writing** as requestor; agreed target locale (chosen via
  the PLAN prioritization rule, with reviewer coverage confirmed) and a prioritized catalog scope;
  CLA/DCO path agreed. On completion, related tasks update `requestor` + `verifiedNeed: true`.

`submit-001`
- Submitted through the **project's required channel** (PR/Weblate/Crowdin/Transifex) with
  **CLA/DCO signed** and `contrib-002` green; AI assistance disclosed per the project's policy.
- **No string-freeze/active-churn conflict** at submission time.
- Tracked to **merge commit** and **release tag/changelog** that ships it. **This is the first true
  Definition of Shipped event** (merged **and** released, reaching users).

**M2 Definition of Done:** upstream maintainer consent secured (`verifiedNeed=true`); **≥ 1
reviewed, lint-clean, compliant translation set submitted, MERGED, and INCLUDED IN A RELEASE.**

---

## Milestone M3 — Scale program

Goal: scale across projects/locales with sustained quality and tracked outcomes.

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| localization-for-good-scale-001 | Translate additional catalogs across ≥2 projects and ≥2 locales (merged+released) | translation | large | medium | translation | submit-001 | Qualified reviewers |
| localization-for-good-rotation-001 | Reviewer rotation + load balancing (burnout prevention) | maintenance | small | low | document | reviewers-002 | Maintainer |
| localization-for-good-outcomes-001 | Outcome tracking: post-merge defect log, release inclusion, locale completion lift, maintainer feedback | data | small | low | dataset | submit-001 | Steward |
| localization-for-good-resync-001 | Source-sync / staleness maintenance cadence (re-translate fuzzied strings) | maintenance | small | low | document | watcher-001, submit-001 | Maintainer |

**Acceptance criteria — key M3 tasks**

`outcomes-001`
- A maintained log capturing, per delivered set: merge + **release** status, **post-merge critical
  defects** (target 0), **locale completion lift**, and maintainer usefulness feedback; feeds
  PLAN.md success metrics.

`resync-001`
- A documented cadence (driven by `watcher-001`) to detect upstream source changes, re-translate
  fuzzied/new strings through the same lint + review gates, and coordinate with upstream
  string-freeze/release timing; defines a withdrawal procedure if a project changes to a non-open
  license or withdraws consent.

**M3 Definition of Done:** **≥ 3 sets merged+released across ≥ 2 projects and ≥ 2 languages**;
reviewer rotation operating; outcome tracking live; source-sync/staleness cadence in effect; named
sustainability owner.

---

## Backlog / future

Sized but unscheduled:

- **(large) `i18n-enablement`** — help a project *add* internationalization (string extraction,
  framework wiring) where it lacks it. Separate engineering effort; own risk profile; out of v0.1.
- **(medium) Short in-app help / microcopy docs** — extend beyond UI catalogs to brief in-app help,
  under a documented review bar. Out of v0.1.
- **(medium) Screenshot/context capture tooling** — surface UI context/screenshots to translators
  for ambiguous strings.
- **(medium) Pseudolocalization pass** — generate pseudo-locales to surface hard-coded/un-externalized
  strings and length-overflow issues to report upstream (we report, we don't refactor).
- **(small) Glossary/TM publishing** — release the accumulated open glossaries + TMX as a reusable
  public good (overlaps `translation-memory-commons` in the portfolio).
- ~~Source-change watcher~~ — **promoted to M0/M1** (`watcher-001`); no longer backlog.
- ~~Back-translation QA~~ — **promoted to a mandatory gate** for `riskDomain`≠`none` strings; no
  longer backlog.
- **(large, funded — needs escrow) Surge drafting under funded lane** — metered drafting against a
  hard per-task budget for high-demand events; would require `fundedBudgetUsd`; out of scope v0.1.

---

## Example task JSON

Schema-valid Task JSON for the first M0 task. `verifiedNeed` is **false** (no consenting upstream
secured); `outputLicense` is **MIT** because the registry is project metadata, not translated
upstream content (translated catalogs inherit the upstream project's license instead).

```json
{
  "id": "localization-for-good-registry-001",
  "title": "Project eligibility rubric + vetted-project registry",
  "project": "localization-for-good",
  "type": "data",
  "lane": "donated",
  "priority": "high",
  "domain": ["localization", "i18n", "open-source", "licensing"],
  "riskTier": "low",
  "urgent": false,
  "deliverable": "dataset",
  "tokenEstimate": "medium",
  "status": "open",
  "context": "localization-for-good translates the UI strings of high-impact nonprofit/civic open-source tools, but only where the upstream project welcomes contributions and we follow its license + CLA/DCO + contribution process. Nothing may be translated or submitted until the target project is vetted and recorded with its license, i18n format, contribution channel, CLA/DCO requirement, risk domain, pinned source commit, and maintainer-consent status. This registry is the consent-first, license-as-data foundation that every later gate checks against.",
  "objective": "Create an objective eligibility rubric and a structured registry of vetted upstream projects, recording per project the fields needed by the contribution-compliance and license gates, so no unsolicited or non-compliant submission can occur.",
  "acceptanceCriteria": [
    "projects/registry.yaml lists at least 5 vetted projects, each scored against the 4-criterion eligibility rubric (open license; public-good mission not for-profit-primary; real language-blocked users; accepts translation contributions)",
    "Each entry records name, repo, url, mission, sector, license, i18nFormat, contributionChannel, claType and claUrl, styleGuideUrl, glossaryUrl, riskDomain, sourceCommit and sourceCatalogHash, maintainerConsent and consentEvidenceUrl, verifiedBy, verifiedDate",
    "Repos with no license or whose primary beneficiary is a for-profit are excluded with a recorded excludedReason",
    "Any project whose strings carry medical, legal, or safety content is flagged riskDomain != none and marked high risk tier",
    "The file validates against registrySchema and passes the CI structural check"
  ],
  "resources": [
    "C:/code/elyos/planning/projects/localization-for-good/PLAN.md",
    "C:/code/elyos/docs/good-deed-definition.md",
    "C:/code/elyos/packages/schema/src/schemas.ts",
    "Unicode CLDR plural rules reference",
    "Developer Certificate of Origin (DCO) 1.1"
  ],
  "output": "projects/registry.yaml plus a short README documenting the eligibility rubric and verification process",
  "requestor": "TO BE SECURED",
  "verifiedNeed": false,
  "outputLicense": "MIT"
}
```
