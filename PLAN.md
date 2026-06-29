# PLAN — localization-for-good

> Status: Draft · Version: 0.1.0 · Last updated: 2026-06-28 · Owner: J. Carter (acting maintainer) · Lane: donated

## Executive summary

`localization-for-good` is an Elyos good-deed project that translates the **user-interface
strings (i18n message catalogs)** of **high-impact nonprofit and civic open-source software**
into languages where the tool is currently unusable or only partly usable, so that real people
can use vital civic/humanitarian software **in their own language**. It targets the strings a
person actually reads on screen — menus, buttons, error messages, form labels, onboarding — for
tools that serve the public good (humanitarian response, civic participation, accessibility,
education, public health, digital rights) and that are genuinely open source.

The defining constraint — and the project's identity — is **"we never dump unsolicited
machine-translation PRs on maintainers."** Drive-by AI translations are a well-documented harm to
open-source maintainers: they create review burden, introduce subtle placeholder/plural bugs, and
erode trust. This project does the opposite. It works **only where the upstream project welcomes
translation contributions**, follows **each project's own license + contribution/CLA process
exactly**, gates every string set behind a **qualified native-speaker reviewer** (the project is
**medium risk tier**), and runs an **automated i18n correctness lint** (placeholder parity, ICU
plural categories per CLDR, markup/escaping) as a hard gate before any human time is spent.

The work runs in the **donated lane**: a human runs their own coding agent interactively to draft
translations, run the i18n lint, and prepare a contribution; the Elyos CLI only prepares the
workspace and opens the PR (or stages the platform upload). The CLI never invokes a coding agent
and never runs headless.

A second defining constraint is **upstream contribution discipline**. Different projects accept
translations in different ways — direct GitHub PR, Weblate, Crowdin, Transifex, or a custom
flow — and most require a **DCO sign-off or a CLA**, plus a style guide and a string-freeze /
release cadence. We treat all of that as **structured, verified data per project** and refuse to
submit anything that does not satisfy it.

Honest status note: the program is real but **no upstream project's maintainer has yet consented**
to be our first delivery target, and **no native-speaker reviewers are yet secured**. Until they
are, `verifiedNeed` is recorded as `false` on tasks whose value depends on a named upstream
beneficiary, and the project-specific Definition of Shipped (merged **and released to users**)
cannot be met. Securing a first consenting upstream project and a first reviewer pair are the top
open dependencies (see Open questions and Roadmap M0–M2).

## Problem & beneficiaries

**Who is helped (ultimate beneficiaries).** People who need a piece of public-good software but
cannot use it because it is not available in a language they read — e.g., a community organizer
using a civic-participation tool, a field worker using a humanitarian mapping or case-management
tool, a low-vision user relying on an accessibility tool's screen-reader labels, a learner using
an open education app. The benefit is **access**: software that does real-world good becoming
usable by people previously locked out by language.

**Secondary beneficiaries.** The **maintainers** of small nonprofit/civic OSS projects, who
rarely have localization capacity. Done right (consent-first, correct, reviewed, low-burden),
this *reduces* their workload instead of adding to it.

**The need.** Most small public-good OSS projects are English-first and chronically
under-localized. Existing locales are often partial, stale (the UI changed but the translation
didn't), or machine-translated without review — which in i18n is uniquely dangerous: a single
corrupted placeholder (`{count}`, `%1$s`, `<a>`) or a missing plural category can break the
screen or render a sentence nonsensical. The gap is **accurate, fluent, reviewed, correctly-
formatted** translations contributed **through the project's own process** by people who respect
the maintainers' time.

**Verified need: TO BE SECURED.** The *category* need (under-localized public-good OSS) is
well-evidenced. But a **specific consenting upstream project, target language(s), and agreed
scope are not yet secured.** Tasks are written so the program can build reusable foundations
(eligibility rubric, format support, i18n-lint, glossary/TM, reviewer + contribution checklists)
**without** an upstream partner, while flagging that **submission/merge tasks are blocked** until
a maintainer consents and a native reviewer is in place.

**Partner / requestor: TO BE SECURED.** In this project the "partner/requestor" is the **upstream
project's maintainer(s)**, whose **consent and contribution process** define and authorize the
work. Candidate sources of first partners: maintainers of humanitarian/civic OSS (e.g. HOT/OSM
ecosystem tools, civic-tech tools, accessibility tools, open-education apps), and existing
localization communities (Mozilla L10n, translatewiki.net, Weblate's hosted libre projects,
Localization Lab) as routes to both consenting projects and qualified reviewers. None is
committed as of this draft.

## Goals and non-goals

**Goals**
- Stand up a **repeatable, consent-first pipeline** that takes a vetted upstream project's source
  catalog and produces reviewed, lint-clean translations submitted **through the project's own
  contribution process**, with full provenance.
- Maintain a **project eligibility rubric + registry** recording, per upstream project, its
  license, i18n format, contribution channel, CLA/DCO requirement, style guide, maintainer
  consent, and risk domain.
- Support the **major i18n formats** (Gettext PO/POT, JSON/ICU, Fluent, Android, Apple, XLIFF,
  Rails YAML) with **format-specific correctness lints**.
- Guarantee **i18n correctness** (placeholder/variable parity, complete CLDR plural categories,
  balanced markup, correct escaping/encoding) via an **automated hard gate** before human review.
- Guarantee **qualified native-speaker review** of every string set before submission (medium
  risk tier), with per-language-pair **glossary + translation memory (TMX)** for consistency.
- Deliver translations that upstream projects **actually merge and ship in a release** so end
  users receive the localized UI.

**Non-goals**
- **Not** unsolicited mass PRs or machine-translation dumps. We do **not** submit to any project
  that has not consented and whose process we have not verified. This is the project's identity.
- **Not** a translation platform / SaaS / hosted Weblate; we use upstream projects' existing
  channels, we do not run infrastructure for them.
- **Not** translating proprietary/closed software or any product whose **primary** beneficiary is
  a for-profit entity (per Elyos guardrails).
- **Not** *adding* internationalization to apps that lack it (extracting strings, wiring i18n
  frameworks) — that is a separate engineering effort (backlog candidate: `i18n-enablement`).
- **Not** translating long-form **documentation, marketing, or user-generated content** — scope
  is **UI message catalogs**. (Docs are a sized backlog item, not v0.1.)
- **Not** authoring new source strings or changing the meaning/behavior of the app.
- **Not** layout/RTL engineering, font work, or fixing the app's own i18n bugs beyond reporting
  them.
- **Not** legal/medical/financial **advice**. If a tool's strings carry such content, we preserve
  its disclaimers verbatim, require expert review, and never relicense or soften them.

## Success metrics (outcomes)

Outcome-based and beneficiary-centric. Baselines are ~0 (new program). **Outcome** targets are
for the first ~6 months after a first consenting upstream + reviewer are secured; **interim
foundation metrics** are tracked from M0/M1 so progress is visible *before* an upstream partner
exists. We explicitly **do not** count "PRs opened," "strings translated," or "lines changed" as
success — only **reviewed, correctly-formatted translations merged upstream and shipped in a
release that reaches users.**

**Outcome metrics (post-upstream-partner)**

| Metric | Baseline | Target | How measured |
|---|---|---|---|
| Translation sets **merged upstream AND included in a released version** | 0 | ≥ 3 (across ≥ 1 project) | Upstream merge commit + release tag/changelog |
| Public-good OSS projects localized end-to-end (consent → reviewed → merged → released) | 0 | ≥ 2 projects | Project registry |
| Languages delivered end-to-end | 0 | ≥ 2 | Project registry |
| Locale **completion lift** on a real project (e.g. crossing a usable threshold in a language) | per-project baseline | ≥ +25 percentage points or crossing the project's "usable" bar in ≥ 1 locale | Upstream catalog stats before/after |
| Translations passing **native-reviewer sign-off on first or second pass** | n/a | ≥ 90% (**effective from M1**) | Reviewer checklist outcomes |
| **i18n-lint compliance** of submitted sets (placeholder/plural/markup) | n/a | 100% (hard gate; **automated from M1**, manual+minimal-lint in M0) | i18n-lint task / CI per deliverable |
| **Contribution-compliance** of submissions (CLA/DCO signed, correct channel, style guide) | n/a | 100% (hard gate) | Contribution checklist per deliverable |
| **Critical post-merge defects** (broken placeholder, missing plural, app crash from a string) | n/a | 0 | Post-merge defect log / upstream issues |
| Maintainer-reported **usefulness / acceptance** of submissions | n/a | Positive from ≥ 1 maintainer | Maintainer feedback |

**Interim foundation metrics (M0/M1, partner-independent)**

| Metric | Baseline | Target | How measured |
|---|---|---|---|
| Eligible upstream projects **vetted + recorded** (rubric + registry) | 0 | ≥ 5 by end of M0 | Registry entries with `verifiedBy`/`verifiedDate` |
| i18n **formats supported** with lint rules | 0 | ≥ 3 by end of M0 (≥ 5 by M1) | Format support matrix + passing lint tests |
| Glossary **terms** captured (per language pair) | 0 | ≥ 30 per pair | Glossary file entry count |
| Native-speaker reviewers **qualified + onboarded** | 0 | ≥ 2 by end of M1 | Reviewer roster |
| **Cycle time** per string set (intake → lint-clean → reviewer sign-off → submission-ready) | n/a | Tracked; trend down | Timestamps on PR/review artifacts |

**Denominator & sample rule for the "≥ 90% pass" rate.** The denominator is **every translation
set that reaches qualified review** (counted once per set, not per pass); a set "passes" if it
earns reviewer sign-off on its **first or second** pass. The rate is reported **only once the
denominator is ≥ 10** reviewed sets; below that we report the raw count (e.g., "7/8 passed")
rather than a percentage to avoid small-sample noise.

## Scope

**Definition — "high-impact nonprofit/civic open-source tool" (objective eligibility rubric).** A
candidate upstream project qualifies only if **all** of the following hold (verified and recorded
in the registry): (a) **genuinely open source** under an OSI-approved license (or the content
strings are under an open/CC license); (b) its **primary mission is public good** —
humanitarian, civic participation/transparency, accessibility, education, public health, digital
rights, or similar — and it is **not primarily operated for a for-profit's benefit** (per Elyos
guardrail #4); (c) it has **real or near-term users who are blocked by language** (evidence of an
under-served locale and a plausible user base, not a dormant repo); and (d) the project
**accepts external translation contributions** (has an i18n setup and a contribution path). A
project that fails any criterion is **excluded with a recorded reason**.

**Definition — "target locale to translate" (priority order).** Among eligible projects/locales,
prioritize: (1) the **secured upstream maintainer's** stated priority locale; (2) the locale with
the **largest blocked user population and weakest current coverage** for a high-impact tool; (3)
locales where a **qualified native reviewer is already available** (reviewer availability is a
**hard precondition**, not a tiebreaker — we never submit unreviewed strings); (4) locales where
**glossary/TM already exists** to reuse. Ties break toward reusing existing glossary/TM work.

**In scope**
- Project **eligibility rubric + registry** (license, format, contribution channel, CLA/DCO,
  style guide, maintainer consent, risk domain, source commit pin).
- Translation of **UI message catalogs** for vetted projects into named target locales, decomposed
  per *project × locale × catalog*.
- **i18n correctness lint** (placeholder parity, CLDR plural-category completeness, markup/tag
  balance, escaping, encoding, length budgets where declared) — per supported format.
- Per-language-pair **glossary** + **translation memory (TMX)**; fuzzy matches surfaced to humans,
  never auto-accepted.
- **Reviewer checklist** (native-speaker, accuracy/fluency/register/cultural fit) and
  **contribution-compliance checklist** (CLA/DCO, channel, style guide, attribution); sign-off
  recorded in the PR/artifact.
- Submission through the **project's own channel** and tracking through **merge + release**.

**Out of scope**
- Any project not passing the eligibility rubric, or any submission without **maintainer consent**
  and a verified contribution process.
- Adding i18n to non-internationalized apps; layout/RTL/font engineering; fixing upstream i18n bugs
  (we report, we don't refactor their framework).
- Documentation, marketing copy, blog posts, or user-generated content.
- Authoring new strings or altering app meaning; "improving" source English.
- Running translation infrastructure (Weblate/Transifex/Crowdin hosting) for others.
- Locales where **no qualified native reviewer** can be sourced (we will not ship unreviewed
  translations — see Risks).
- Translating, softening, or relicensing **high-stakes advice** content (medical/legal/safety)
  without the required expert review and verbatim disclaimer preservation.

## Solution approach & architecture

Primarily a **content/data pipeline** project (deliverables are translated catalogs + supporting
data), with **light, agent-neutral tooling** (the i18n-lint + schema validation). It rides on
existing Elyos donated-lane mechanics: CLI prepares the workspace, the human runs their agent, a
PR is opened (or a platform upload is staged), and human/expert review gates "done."

**Pipeline (per project × locale × catalog)**
1. **Vet & verify upstream project** — confirm it passes the eligibility rubric; record license,
   i18n format, **contribution channel** (PR/Weblate/Crowdin/Transifex), **CLA/DCO** requirement,
   style guide, glossary, **risk domain** (none/medical/legal/safety), and **maintainer consent**.
   **Pin the source catalog commit + hash.**
2. **Intake** — fetch the source catalog at the pinned commit; load glossary + TM for the pair;
   identify untranslated and **fuzzy** strings; collect context (translator comments, screenshots
   where available) for ambiguous strings.
3. **Draft translation** — agent translates, **preserving every placeholder/variable, ICU plural
   structure, and markup tag exactly**, applying the glossary, TM, and the project's style guide
   (register/formality, terminology). Fuzzy TM matches are **flagged for human decision**, never
   auto-accepted.
4. **i18n correctness lint (automated hard gate)** — placeholder/variable parity vs. source;
   **all required CLDR plural categories present** for the target locale; markup/tag balance;
   correct escaping/encoding (UTF-8); declared length budgets respected. **Failing lint blocks
   human review** (don't spend reviewer time on broken output).
5. **Agent self-check + uncertainty flags** — agent runs the reviewer checklist as a first pass
   and **emits `UNCERTAIN:` flags** for low-confidence strings into `review.yaml`.
6. **Qualified native-speaker review** — a reviewer independent of the drafting step verifies
   accuracy, fluency, register/formality, terminology, and cultural fit, resolves every agent
   flag, completes the checklist, and **records sign-off** (+ COI declaration). For
   safety/legal/medical-domain strings: **second reviewer + back-translation QA + expert sign-off**.
7. **Contribution-compliance check** — CLA/DCO signed; correct channel; style guide followed;
   attribution/provenance present; **no source string under active churn / string freeze conflict**.
8. **Submit, merge, release** — submit via the project's channel; shepherd the review; record the
   **merge commit and the release** that ships it; capture maintainer feedback + any post-merge
   defects.

**Artifacts / data model**
- `projects/registry.yaml` — array of vetted upstream entries:
  `{ id, name, repo, url, mission, sector(humanitarian|civic|accessibility|education|public-health|digital-rights),
  license, i18nFormat, contributionChannel(pr|weblate|crowdin|transifex|other), claType(dco|cla|none),
  claUrl, styleGuideUrl, glossaryUrl, screenshotsAvailable, riskDomain(none|medical|legal|safety),
  sourceCommit, sourceCatalogHash, maintainerConsent(none|requested|granted|declined), consentEvidenceUrl,
  verifiedBy, verifiedDate, excludedReason }`.
- `glossaries/<project>-<src>-<tgt>.yaml` — `{ term, translation|preserve, partOfSpeech, register, notes }`
  plus terminology + do-not-translate lists.
- `tm/<src>-<tgt>.tmx` — open **translation memory** (TMX) reused across projects of the same pair.
- `translations/<project>/<locale>/` — the translated catalog(s) in the project's native format,
  `provenance.yaml`, `review.yaml` (checklist + sign-off + `agentFlags`), `contribution.yaml`
  (channel, CLA/DCO status, source commit, attribution).
- `templates/reviewer-checklist.md`, `templates/contribution-checklist.md`,
  `templates/style-guide-template.md`.

**Supported i18n formats (support matrix).** Gettext **PO/POT**, **JSON** (i18next/react-intl) +
**ICU MessageFormat**, Mozilla **Fluent (.ftl)**, **Android** `strings.xml` (incl. `plurals`),
**Apple** `.strings`/`.stringsdict`, **XLIFF**, **Rails YAML**. Each format has a parser +
format-specific lint rules (e.g. `%1$s` positional args for Android; `{name}` ICU for JSON;
`.stringsdict` plural variants for Apple). UTF-8 throughout.

**Content schemas & CI validation.** The Task JSON schema lives in
`packages/schema/src/schemas.ts` (AJV / JSON Schema **draft-07**). This project's content
artifacts get their **own JSON Schemas in the same place** — `registrySchema` (project registry),
`glossarySchema`, `reviewSchema`, `contributionSchema` — compiled and exposed via `validate.ts`
exactly like `taskSchema`. A structural-check script (`tooling-001`) parses the YAML artifacts to
JSON and validates them, wired into `pnpm test` so **CI fails on any malformed content file**. The
**i18n-lint** (`lint-001`/`lint-002`) is a separate agent-neutral package step that operates on the
translated catalogs themselves. Keeping validation in the core schema package (not adapters)
preserves the agent-neutral-core rule.

**Key decisions**
- **Consent-first, registry-as-data.** Maintainer consent and contribution process are encoded as
  **structured, checkable data**, so the contribution-compliance gate is verifiable, not vibes.
- **Lint-before-human.** The automated i18n correctness gate runs *before* reviewer time is spent —
  protecting both correctness and scarce human reviewers.
- **Glossary + TM driven** drafting for terminology consistency across a project and across
  projects sharing a language pair; **fuzzy matches never auto-accepted.**
- **Output license follows upstream.** Translations are derivative works contributed under the
  **upstream project's license + its CLA/DCO** — *not* a blanket relicense. Project **tooling/code
  is MIT**; that does not extend to the translated strings, which inherit upstream terms.
- **No data ingestion beyond public source catalogs** — minimal privacy/PII surface; no UGC, no
  user data.

## Data, licensing & compliance

**This is the project's most important section. Be conservative; when terms are unclear, do not
submit.**

**What we ingest.** Only **public source message catalogs** of vetted open-source projects, pulled
**at a pinned commit** and **content-hashed**. No user data, no user-generated content, no
analytics, no scraping beyond the catalog files themselves.

**License model — translations are derivative works of the upstream project.** A UI string
catalog is part of the project's codebase and is covered by **its** license (MIT, Apache-2.0,
GPL/AGPL, MPL, etc.). Our translations are **derivative works** and are contributed back **under
that same license** through the project's process. We **do not** relicense or attach an
incompatible license to translated strings. Implications we verify and record per project:
- **OSI-approved license confirmed** (or, for content-string projects, an open/CC license). Repos
  with **no license** are **excluded** (no implied right to contribute/distribute).
- **CLA vs. DCO.** Many projects require a **Developer Certificate of Origin** sign-off
  (`git commit -s`) or a **Contributor License Agreement**. We **record the exact requirement**,
  **sign it as required**, and **do not submit** where we cannot satisfy it. The human contributor
  signs in their own name; AI assistance is disclosed per the project's policy if one exists.
- **Copyleft (GPL/AGPL/MPL).** Contributing translations into copyleft projects is fine (same
  license); we never extract their strings into a differently-licensed artifact.
- **Platform terms.** Where a project localizes via **Crowdin/Transifex/Weblate**, we verify the
  **platform's contributor terms** and the project's chosen license still govern the strings; we
  **prefer open channels (Weblate / direct PR)** and flag any platform term that conflicts with
  open principles.

**Provenance & reproducibility.** Every deliverable ships `provenance.yaml` linking it to: the
upstream project + **source commit + catalog hash**, the glossary + TM versions, the translator
(agent + human), and the reviewer + sign-off. The **pinned commit + hash** make the work
reproducible and let a **source-sync watcher** (M0 minimal/manual, M1 automated) detect when
upstream source strings change so translations don't silently go stale/fuzzy.

**BLOCKING prerequisite before the first translation drafts (`license-000`).** Before
`translate-001` may start, the maintainer/compliance reviewer must have **confirmed and recorded
in the registry entry**: (a) the project is **eligible** (rubric passed) and **open-licensed**;
(b) the **contribution channel + CLA/DCO requirement** are known and satisfiable; and (c)
**maintainer consent** to receive translation contributions exists (at minimum: documented
evidence the project welcomes translation contributions; ideally an explicit invitation for the
chosen locale). If any is unconfirmed, that project is **not** drafted and a different eligible
project is selected. This is a **hard gate, not an open question.**

**Privacy / PII.** UI strings should contain no personal data; we **verify no PII is present or
introduced**, ingest no user data, and keep any maintainer contact details out-of-band and
uncommitted. We never translate user-generated content.

**Non-partisan stance (civic tools).** For civic/governance tools, translations must use a
**neutral register**, faithfully render the source meaning, and introduce **no partisan framing,
slant, or editorializing**. This is a reviewer checklist item.

**High-stakes content within a tool.** If a tool's strings carry **medical/legal/safety** content
(e.g. a first-aid app, a know-your-rights tool), the project is flagged `riskDomain` ≠ `none`,
the strings are **high risk tier**, a **credentialed expert** must sign off, **disclaimers are
preserved verbatim** ("not advice"), and meaning is never softened or altered in translation.

**Attribution.** Submissions follow the project's attribution/credits convention (e.g.
`CONTRIBUTORS`, `TRANSLATORS`, commit trailers); we add no extra attribution the project doesn't
use.

## Quality, review & risk gates

**Risk tier: medium** (per the good-deed definition: needs domain accuracy + a **reviewer with the
relevant skill** — here a **qualified native/near-native speaker of the target language**).
**Escalates to high** for any project whose strings carry medical/legal/safety advice content
(`riskDomain` ≠ `none`) — those require **credentialed expert sign-off** and verbatim disclaimer
preservation.

**Required gates before a deed is "done" (in order)**
1. **i18n correctness lint** — automated hard gate (placeholder/variable parity, complete CLDR
   plural categories, markup/tag balance, escaping/encoding, declared length budgets). **Lint must
   pass before human review.**
2. **Agent self-check** against the reviewer checklist, emitting `UNCERTAIN:` flags into
   `review.yaml`.
3. **Qualified native-speaker reviewer** sign-off recorded in the PR/artifact (accuracy, fluency,
   register/formality, terminology, cultural fit, non-partisan for civic). Reviewer is
   **independent of the drafting step**; COI declared. Every agent flag resolved
   (`resolved`/`accepted-as-is` with reasoning) — **unresolved flags block sign-off.**
4. **Safety-domain escalation** — for `riskDomain` ≠ `none` strings: **second independent
   reviewer + back-translation QA + credentialed expert sign-off**; disclaimers verbatim.
5. **Contribution-compliance check** — CLA/DCO signed, correct channel, style guide followed,
   provenance + attribution present, **no string-freeze/active-churn conflict**.
6. **CI green** for tooling + structural checks; i18n-lint green on the deliverable.
7. **Maintainer approval** of the Elyos-side PR.

**Reviewer independence & two-reviewer rule.** The human who ran the drafting agent may **not** be
the sole sign-off reviewer. A **second independent qualified reviewer is mandatory** for
safety/legal/medical-domain strings; single-reviewer sign-off is allowed only for ordinary UI
strings with no such content.

**Reviewer disagreement / conflict resolution.** If reviewers disagree, the set **cannot be signed
off** until resolved: (1) reconcile against source, glossary, and style guide, recording rationale;
(2) escalate unresolved cases to a **third reviewer or the maintainer**; (3) when in doubt, the
**more conservative reading wins** and the disputed string is **held back** rather than shipped.
Recorded in `review.yaml`.

**Agent uncertainty self-check (operationalized).** The drafting agent emits one flag per
low-confidence string: `UNCERTAIN: <stringKey> | <type: term|plural|placeholder|register|cultural|ambiguous-source> | <note>`,
copied into `review.yaml` as `agentFlags`. **No sign-off while any flag is unresolved.**

**Definition of Shipped (project-specific).** A translation set is *shipped* only when: acceptance
criteria met **and** i18n-lint green **and** native-speaker (and, where required, expert) sign-off
recorded **and** contribution-compliance verified (CLA/DCO + channel + style guide) **and**
provenance complete **and** the set is **merged upstream AND included in a released version that
reaches users.** A merged-but-unreleased PR is *delivered*, not *shipped*; an opened-but-unmerged
PR is neither.

## Roadmap & milestones

**M0 — Foundation & cold-start (no upstream partner required).**
Goal: build the eligibility/registry/lint/glossary/review/contribution machinery and prove the
pipeline on one real project's strings into one locale, end-to-end **except** submission/merge.
Exit criteria: eligibility rubric + registry with **≥ 5 vetted projects** (license, format,
channel, CLA/DCO, risk domain, **pinned source commit + hash**, consent status); **i18n-lint
supporting ≥ 3 formats** with passing tests; one language-pair **glossary (≥ 30 terms) + TM**
started; reviewer checklist + contribution-compliance checklist + style-guide template merged;
**`license-000` BLOCKING prerequisite confirmed** (eligible + open-licensed + channel/CLA known +
consent evidence) **before drafting**; **one project's catalog translated into one locale,
lint-clean, with qualified native-speaker sign-off** (submission to upstream deferred to M2).
`verifiedNeed` honestly recorded `false` where no upstream partner. **License/lint-gate note:** to
keep the "100%" hard gates honest from day one, M0 ships a **minimal automated structural check**
(`tooling-001`) + the **i18n-lint** (`lint-001`) that fail CI on malformed metadata or
placeholder/plural/markup errors; the **full per-format lint + CLA/DCO enforcement** is automated
in M1 (`lint-002`, `contrib-002`). The 100% / ≥90% metrics are therefore **effective from M1**.

**M1 — Repeatability & reviewer network.**
Goal: make the pipeline repeatable and recruit/qualify native-speaker reviewers.
Exit criteria: documented reviewer-qualification criteria + **≥ 2 named qualified reviewers** (or a
localization-community partnership, e.g. translatewiki / Weblate libre community / Localization
Lab); glossary + TM generalized to **≥ 2 language pairs**; **i18n-lint covers ≥ 5 formats and is
enforced in CI** (`lint-002`); **CLA/DCO + contribution-compliance enforced** (`contrib-002`);
**automated source-sync watcher** (hash-diff vs. pinned commit) operating; a **second** project's
strings translated + reviewed (still pre-submission if no consent yet). Dependency: reviewer
sourcing.

**M2 — First upstream delivery (needs maintainer consent).**
Goal: get a reviewed translation **merged and released** upstream.
Exit criteria: a first upstream maintainer **consents** in writing (`verifiedNeed = true`); agreed
locale + scope; CLA/DCO signed; **≥ 1 reviewed, lint-clean, compliant translation set submitted
through the project's channel, MERGED, and INCLUDED IN A RELEASE.** First true Definition of
Shipped event. Dependency: M0/M1 + consenting upstream + reviewer for the locale.

**M3 — Scale program.**
Goal: scale across projects/locales with sustained quality and tracked outcomes.
Exit criteria: **≥ 3 sets merged+released across ≥ 2 projects and ≥ 2 languages**; reviewer
rotation established; outcome tracking (post-merge defect log, maintainer feedback, release
inclusion, locale completion lift) operating; **source-sync/staleness maintenance cadence** in
effect; named sustainability owner.

## Work breakdown

The itemized, sized backlog lives in **[TASKS.md](./TASKS.md)**, organized by the milestones above
(M0–M3) plus a Backlog/future section. Each task maps to an Elyos Task JSON (schema in
`packages/schema/src/schemas.ts`) with id, type, lane, risk tier, deliverable, acceptance criteria,
and license/contribution fields. M0–M1 tasks are partner-independent foundations; M2+ tasks are
gated on a consenting upstream and are marked `verifiedNeed: false` until then.

## Governance, roles & stakeholders

- **Maintainer (Owner): J. Carter (acting)** — assigned now; accepts/sequences tasks, approves the
  Elyos-side PRs, owns registry integrity and the contribution-compliance gate. Acts as interim
  license/compliance reviewer until a dedicated one is named.
- **Qualified native-speaker reviewers (per language pair): TO BE SECURED** — native/near-native
  speakers with localization competence; sign-off recorded in PRs. Rotation defined in M1/M3.
- **License/contribution-compliance reviewer** — may be the maintainer initially; verifies
  per-project license, CLA/DCO, channel, and per-deliverable compliance. Escalates ambiguity.
- **Steward (last-mile owner): TBD — named by end of M1** (acting maintainer holds these duties
  interim). Owns the **upstream maintainer relationship**, shepherds the PR, and confirms
  **merge + release** (without this, nothing reaches "shipped"). Naming a steward is a
  **prerequisite for entering M2**.
- **Upstream maintainer / requestor: TO BE SECURED** — consents to and defines the contribution
  (locale, scope, process), reviews/merges, and ships the release. This is the project's "partner."
- **Expert reviewers (high risk tier)** — credentialed medical/legal/safety experts for any
  `riskDomain` ≠ `none` project; required before such strings are submitted.

## Dependencies & integrations

- **Elyos donated lane**: `packages/cli` (workspace prep + PR), `packages/core`, `packages/schema`
  (Task JSON + this project's content schemas). No funded-lane / API-key execution in v0.1.
- **Upstream OSS projects + their repos/catalogs** (read-only at pinned commit; license +
  contribution-process verification per project) — the beneficiary/requestor side.
- **Contribution channels**: GitHub PRs and/or **Weblate / Crowdin / Transifex** (terms verified
  per project; open channels preferred).
- **Native-speaker reviewers / a localization community** (translatewiki.net, Mozilla L10n,
  Weblate libre communities, Localization Lab) — **external dependency, not yet secured.**
- **CLDR plural-rule data** (for the lint's plural-category completeness check) and standard i18n
  format parsers (PO/ICU/Fluent/Android/Apple/XLIFF/YAML), each under their own open licenses.
- **Open glossary/terminology + TMX references** per language, subject to their own licenses.

## Risks & mitigations

| Risk | Likelihood | Impact | Mitigation | Owner |
|---|---|---|---|---|
| Unsolicited AI-translation PRs burden maintainers / harm Elyos reputation | High | High | **Consent-first identity**: never submit without verified maintainer consent + process (`license-000` blocking gate); lint-before-human so only correct work is offered | Maintainer / Steward |
| Placeholder/variable corruption (`{count}`, `%1$s`, `<a>`) breaks the app | Medium | Critical | **Automated i18n-lint hard gate** (parity check) before human review + reviewer check; 0 post-merge defect target | Maintainer / Reviewers |
| Missing/incorrect **CLDR plural categories** for target locale | Medium | High | Lint verifies all required plural categories present per CLDR; reviewer confirms wording | Maintainer / Reviewers |
| **CLA/DCO or license** non-compliance on submission | Medium | High | Per-project registry records exact requirement; **contribution-compliance gate**; sign DCO/CLA; exclude no-license repos; "if unclear, don't submit" | Compliance reviewer |
| Poor MT fluency/accuracy reaches users | Medium | High | **Mandatory native-speaker review**; glossary + TM; agent output is draft only; `UNCERTAIN` flags block sign-off | Reviewers |
| No qualified native reviewer for a locale | High | High | Don't submit unreviewed; partner with localization community; only schedule locales with reviewer coverage | Steward / Maintainer |
| Source strings change → translations go **stale/fuzzy** | Medium | Medium | Pin source commit + hash; **source-sync watcher** flags drift; re-sync cadence; coordinate with upstream release/string-freeze schedule | Maintainer |
| String-freeze / release-timing conflict (translating churning strings) | Medium | Medium | Coordinate with upstream freeze/release cadence; don't translate strings under active churn; contribution-compliance checks timing | Steward |
| No consenting upstream → nothing reaches "shipped" | High | High | M0/M1 build partner-independent value; **concrete outreach plan** (Open questions) with owner+timeline; `verifiedNeed=false` until secured; **pause/decision point** if none by end of M1 | Acting maintainer → Steward |
| Register/formality (T-V), gender, or cultural errors | Medium | Medium | Native-speaker review + per-locale style guide; register field in glossary; cultural-fit checklist item | Reviewers |
| Civic-tool translation introduces partisan slant/bias | Low | High | Non-partisan neutral-register rule; reviewer checklist item; faithful-to-source mandate | Reviewers / Maintainer |
| Medical/legal/safety strings mistranslated or disclaimers softened | Low | Critical | `riskDomain` flag → **high tier**: expert sign-off + back-translation + **disclaimers verbatim**, meaning never altered | Expert reviewer / Maintainer |
| RTL/bidi or encoding issues | Medium | Medium | UTF-8 enforced; lint checks encoding/bidi markers; reviewer with RTL competence for RTL locales | Reviewers / Maintainer |
| Platform terms (Crowdin/Transifex) conflict with open principles | Low | Medium | Verify platform contributor terms per project; **prefer Weblate/direct PR**; flag conflicts | Compliance reviewer |
| Agent overconfidence / unflagged uncertainty | Medium | High | Operationalized `UNCERTAIN:` flags into `review.yaml`; unresolved flags **block** sign-off | Reviewers |

## Security & privacy

- **Threat surface** is small: read-only ingestion of public source catalogs + text artifacts in a
  public repo. Main risks are **integrity** (wrong/tampered source, corrupted placeholders) and
  **compliance** (license/CLA), not data exfiltration.
- **No secrets** in the normal flow (donated lane uses the human's own agent and their own
  GitHub/platform credentials; no API keys/escrow). Per CLAUDE.md, never write secrets/tokens into
  logs, receipts, or committed files.
- **PII**: UI strings are verified to contain no personal data; we ingest no user data and never
  translate user-generated content. Maintainer contacts kept out-of-band and uncommitted.
- **Abuse/misuse prevention**: refusal guardrails apply — refuse tasks that would translate for a
  for-profit's primary benefit, introduce misinformation/partisan slant, deceive users, give
  unqualified high-stakes advice, or violate a project's license/CLA/privacy.
- **Supply-chain integrity**: **pin source commit + content hash**; run the source-sync watcher
  (hash-diff) to detect later changes; verify the upstream repo/catalog is authentic before
  translating.

## Sustainability & maintenance

- **After merge+release**, translations must be **kept in sync** as the source UI evolves (new
  strings, changed strings → fuzzy). The maintainer + steward run a **re-sync cadence** (driven by
  the source-sync watcher) so locales don't decay — this is the dominant long-term cost in
  localization and is a first-class part of the plan, not an afterthought.
- **Reviewer rotation** (M1/M3) and a localization-community partnership avoid single-person
  dependence and reviewer burnout.
- **Outcome tracking** continues post-merge: a defect/feedback log per delivered set, release-
  inclusion confirmation, locale-completion tracking, and periodic maintainer check-ins. Outcomes
  (merged+released, post-merge defects, completion lift, maintainer usefulness) — not PR counts —
  are the maintained metrics.
- **Decommissioning**: if a project changes its license to non-open, removes its i18n setup, or
  withdraws consent, we stop contributing and record the change; provenance makes impact assessable.

## Open questions

1. **First consenting upstream**: which public-good OSS project is the first requestor, and what
   locale + scope do they want? (Blocks M2 and `verifiedNeed=true`.) **Outreach plan (concrete):**
   - **Target sources (named):** maintainers of humanitarian/civic/accessibility/open-education
     OSS; localization communities as routing (translatewiki.net, Mozilla L10n, Weblate libre
     community, Localization Lab) — these also yield reviewers.
   - **Owner:** the **acting maintainer** runs outreach until a Steward is named (end of M1).
   - **Timeline:** begin outreach in parallel with M0; aim for **≥ 3 serious maintainer
     conversations by end of M1** and a **first consenting project during M2** (`partner-001`).
   - **Pause/decision point:** if **no upstream consents by end of M1**, the program **pauses new
     translation work** and the maintainer makes an explicit **go / pivot / hold** decision rather
     than producing translations no project has agreed to receive.
2. **Reviewer sourcing**: recruit individual native-speaker reviewers or partner with a
   localization community? What are the formal qualification criteria (and how do we handle locales
   with thin reviewer pools)?
3. **Contribution channels**: how many platforms (PR vs. Weblate vs. Crowdin vs. Transifex) do we
   support in v0.1, and which platform terms are acceptable under our open principles?
4. **AI-disclosure policy**: how do we disclose AI assistance to upstream projects that have (or
   lack) an AI-contribution policy, consistently and honestly?
5. **Docs vs. UI scope**: do we ever extend beyond UI catalogs to short in-app help/docs, and
   under what review bar? (Out of scope for v0.1.)
6. **Funded lane?** Proposal implies donated; do we ever want metered drafting under escrow for
   surge demand (with a hard per-task budget)? (Out of scope for v0.1.)

## References

- `C:\code\elyos\CLAUDE.md` — Elyos work rules, lanes, quality bar, refusal guardrails.
- `C:\code\elyos\docs\good-deed-definition.md` — good-deed criteria and risk tiers.
- `C:\code\elyos\packages\schema\src\schemas.ts` — Task JSON schema.
- `C:\code\elyos\planning\ROADMAP.md` — portfolio roadmap (localization-for-good, Track 4).
- `C:\code\elyos\planning\projects\vital-info-translations\` — sibling translation project (license/review discipline reference).
- Unicode **CLDR plural rules** (for plural-category completeness checks) — verify current data.
- i18n format references: GNU **gettext** (PO), **ICU MessageFormat**, Mozilla **Fluent**,
  Android string resources, Apple `.strings`/`.stringsdict`, **XLIFF**, Rails YAML.
- Developer Certificate of Origin (DCO) 1.1; per-project CLA terms (verify per project).
- Localization communities (candidate reviewer/partner routes): translatewiki.net, Mozilla L10n,
  Weblate libre community, Localization Lab.

---

## Appendix A — Improvements applied

The following 25 specific improvements were identified against the first draft and **applied** to
this PLAN.md and to TASKS.md (this is the post-improvement version).

1. **Consent-first as the project's identity.** Promoted "we never dump unsolicited MT PRs on
   maintainers" from a buried risk to the executive summary, a top non-goal, the #1 risk, and a
   blocking gate (`license-000` now requires recorded maintainer consent).
2. **Lint-before-human ordering.** Made the automated i18n correctness lint a hard gate that runs
   **before** reviewer time is spent, explicit in the pipeline and Quality gates.
3. **Objective eligibility rubric** for "high-impact nonprofit/civic OSS" (4 testable criteria;
   no-license repos excluded), replacing a vague description.
4. **i18n-specific correctness lint defined** (placeholder/variable parity, CLDR plural-category
   completeness, markup/tag balance, escaping/encoding, length budgets) rather than a generic
   "check quality."
5. **Format support matrix** named (PO, JSON/ICU, Fluent, Android, Apple, XLIFF, Rails YAML) with
   per-format lint rules and a foundation metric (≥3 formats M0, ≥5 M1).
6. **Definition of Shipped = merged AND released** to users, not "PR opened/merged" — added a
   delivered-vs-shipped distinction.
7. **Outcome metrics made measurable** with baselines, targets, and a denominator/sample rule for
   the ≥90% pass rate (mirrors house standard); added a "locale completion lift" outcome.
8. **Pinned source commit + content hash** for reproducibility, plus a **source-sync watcher** to
   detect upstream drift (M0 minimal, M1 automated).
9. **Staleness/re-sync cadence** elevated to a first-class sustainability concern (the dominant
   long-term localization cost) and an M3 milestone, not a footnote.
10. **CLA vs. DCO compliance** handled explicitly: per-project requirement recorded, signed as
    required, no-license/unsatisfiable-CLA projects excluded, blocking before drafting.
11. **License model clarified**: translations are derivative works contributed under the upstream
    license; tooling is MIT but does not extend to strings; copyleft handled.
12. **Platform-terms verification** (Crowdin/Transifex/Weblate) added with a preference for open
    channels and a conflict-flagging rule.
13. **Glossary + Translation Memory (TMX)** added for cross-project terminology consistency;
    **fuzzy matches never auto-accepted** (surfaced to humans).
14. **CLDR plural-category completeness** operationalized as a concrete lint check (a top source of
    real-world i18n bugs).
15. **RTL/bidi + encoding** handling addressed in pipeline, lint, reviewer competence, and risks.
16. **Register/formality (T-V), gender, cultural fit** added as glossary fields + style-guide +
    reviewer checklist items.
17. **String-freeze / release-timing coordination** added as a compliance check and a risk (don't
    translate churning strings).
18. **Non-partisan neutral-register rule** for civic tools made a reviewer checklist item and a
    risk with owner.
19. **High-stakes (medical/legal/safety) escalation**: `riskDomain` flag → high tier → expert
    sign-off + back-translation + **disclaimers verbatim, meaning never altered**.
20. **Agent uncertainty self-check operationalized** (`UNCERTAIN:` flag format → `review.yaml`
    `agentFlags`; unresolved flags block sign-off).
21. **Reviewer independence + two-reviewer rule + conflict resolution** (conservative reading wins)
    specified, matching the house standard.
22. **Maintainer-acceptance / usefulness metric** added; vanity metrics (PRs opened, strings
    translated) explicitly rejected.
23. **Content JSON schemas in `packages/schema`** (`registrySchema`, `glossarySchema`,
    `reviewSchema`, `contributionSchema`) validated in CI, keeping validation agent-neutral.
24. **Pause/decision (go/pivot/hold) gate** if no upstream consents by end of M1, plus a concrete
    outreach plan with owner + timeline.
25. **AI-disclosure honesty** to upstream projects added as a compliance consideration and an open
    question, so contributions are transparent about AI assistance.

## Review sign-off

**Reviewed against the PLAN_SPEC robustness bar on 2026-06-28 (self-review, senior-staff +
TPM lens).** Checks performed and results:

- **Measurable metrics?** Yes — every success metric has baseline + target + measurement method;
  ≥90% pass-rate has a denominator + minimum-sample rule; interim partner-independent metrics let
  progress show before an upstream partner exists.
- **Enforceable gates?** Yes — i18n-lint (automated, lint-before-human), CLA/DCO + contribution-
  compliance, native-speaker (and expert, where required) sign-off, and Definition of Shipped
  (merged **and released**) are all concrete and checkable; `license-000` is a hard blocking gate.
- **Risks with owners + mitigations?** Yes — 14-row table, each with likelihood/impact/mitigation/
  owner; the headline risk (unsolicited MT burden) is mitigated by the consent-first identity.
- **License / PII / expert-review guardrails?** Yes — derivative-work + CLA/DCO model, no-license
  exclusion, platform-terms check, no PII / no UGC, and high-tier expert sign-off + verbatim
  disclaimers for medical/legal/safety strings.
- **Sequencing?** Yes — M0 foundations (no partner) → M1 repeatability + reviewers → M2 first
  merged+released delivery (needs consent) → M3 scale + staleness maintenance; dependencies stated;
  a pause/decision point prevents producing work no one will receive.
- **Schema-valid tasks?** Yes — TASKS.md maps every field to `packages/schema/src/schemas.ts`; the
  example Task JSON uses only enum-valid values and includes all required fields with honest
  `verifiedNeed: false`.

**Issues found and fixed during review:** (a) clarified that the **i18n-lint** is a separate
agent-neutral step from the **schema structural check**, so neither violates the agent-neutral-core
rule; (b) made the *delivered vs. shipped* distinction explicit (merged ≠ released); (c) ensured
the example task's `deliverable` is schema-enum-valid (`dataset`) and that all M2 submission tasks
carry `verifiedNeed: false` until consent flips them true; (d) added reviewer **availability as a
hard precondition** (not a tiebreaker) to the prioritization rule.

**Open human decisions required:** secure a first consenting upstream maintainer + locale; secure
≥2 native-speaker reviewers (or a localization-community partnership); name a Steward before M2;
choose which contribution platforms/terms are acceptable in v0.1; set the AI-disclosure policy.

**Status: Draft v0.1.0 — ready for maintainer review.**
