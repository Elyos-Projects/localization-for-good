# Competitive & Improvement Analysis — `localization-for-good`

> Scope: open localization/translation of high-impact open-source software + nonprofit content into
> under-served languages (UI strings, message catalogs). Reviewed against PLAN.md / TASKS.md v0.1.0
> (2026-06-28). Web-researched competitive grounding with cited sources. Author lens: senior l10n
> engineering + open-source governance.

---

## 1. Correctness & completeness review of PLAN.md

The plan is unusually strong for a v0.1 draft. Its central thesis — **consent-first, never dump
unsolicited MT PRs on maintainers** — is exactly right and is, if anything, *more* timely than the
plan claims. The maintainer-burden problem it cites as "well-documented" is now an acute, named
crisis: the Godot maintainer publicly describes triaging "AI slop" as draining; the Jazzband Python
collective shut down partly over AI-spam volume; and GitHub is reportedly weighing a "kill switch"
for pull requests to stem the deluge ([The New Stack](https://thenewstack.io/ai-generated-code-crisis/),
[The Register](https://www.theregister.com/2026/02/03/github_kill_switch_pull_requests_ai/),
[InfoWorld](https://www.infoworld.com/article/4127156/github-eyes-restrictions-on-pull-requests-to-rein-in-ai-based-code-deluge-on-maintainers.html)).
A field report specifically on *localization* PRs found maintainers "often have no practical way to
evaluate whether the translation is correct, complete, safe to merge, or maintainable across
releases" ([DevOps.com](https://devops.com/what-five-localization-pull-requests-revealed-about-open-source-governance-a-field-report-on-open-sources-i18n-infrastructure-gap/)).
The plan's identity is the correct response to a real and worsening market condition.

**What is correct and complete:**

- **Native-speaker review gate.** Mandatory, independent-of-drafting, two-reviewer rule for
  safety-domain strings, conflict-resolution ("conservative reading wins"), COI declaration. This
  matches and in places exceeds incumbent practice. Strong.
- **MT-post-editing vs raw MT.** Explicitly forbids shipping raw MT; agent output is "draft only";
  fuzzy TM matches "never auto-accepted." Aligns with Mozilla's own stance that LLM/MT tools "are
  designed to support, not replace" experienced localizers
  ([Mozilla L10n](https://blog.mozilla.org/l10n/2024/05/28/transforming-translations-how-llms-can-help-improve-mozillas-pontoon/)). Correct.
- **ICU/plurals/placeholders.** The i18n-lint hard-gate (placeholder parity, CLDR plural-category
  completeness, markup/tag balance, escaping/encoding) before human review is the single best design
  decision in the plan — it is precisely what incumbents enforce (Translate Toolkit's `pofilter` runs
  40+ such checks; Weblate ships configurable quality checks)
  ([pofilter docs](http://docs.translatehouse.org/projects/translate-toolkit/en/latest/commands/pofilter.html),
  [Weblate checks](https://docs.weblate.org/en/latest/admin/checks.html)). Correct and well-sequenced
  ("lint-before-human").
- **String-freeze / context.** Coordinating with upstream string-freeze/release cadence is named as
  a compliance check and a risk; translator context (comments, screenshots) is collected at intake.
  Good — matches KDE's "Scripty" template/merge cadence and translatewiki's twice-weekly export model
  ([KDE l10n](https://l10n.kde.org/), [translatewiki](https://translatewiki.net/wiki/Translating:MediaWiki)).
- **Attribution.** "Follow the project's own convention, add nothing extra" — correct and respectful.
- **Scope discipline.** UI catalogs only; docs/marketing/UGC out; not adding i18n to apps; not
  RTL/font engineering. Tightly bounded and honest about `verifiedNeed: false`.

**Gaps / weaknesses to fix:**

1. **RTL/bidi is under-specified and partly self-contradictory.** The plan lists "layout/RTL/font
   engineering" as out-of-scope, yet the lint and reviewer competence must still handle bidi control
   characters (LRM/RLM/FSI/PDI), placeholder direction in RTL strings, and Unicode bidi isolation
   around interpolated variables. Shipping an Arabic/Hebrew/Farsi string with a mis-isolated
   placeholder *is* a string-level correctness bug, not "layout engineering." Recommend an explicit
   lint rule for bidi-isolation correctness and a stated "we handle string-level bidi, not CSS/layout"
   line. (Note: Localization Lab's core constituency — internet-freedom tools — skews heavily to
   RTL/at-risk-language communities, so this matters for partner fit;
   [Localization Lab](https://www.localizationlab.org/about-us).)

2. **Under-served-language reviewer sourcing is the binding constraint and is still hand-wavy.** The
   plan correctly flags "no reviewers secured" and names community routes, but under-served languages
   are *precisely* the ones with thin or zero professional reviewer pools — that is what "under-served"
   means. The plan's prioritization rule makes reviewer availability a hard precondition (good), but
   that logically pushes the program *away* from the most under-served languages toward those that
   already have reviewers — a quiet mission drift. Needs an explicit answer: for a language with no
   professional reviewers, do we (a) partner with a community org that can vouch for a native speaker
   (Localization Lab's 7,000+ contributors in 220 languages; CLEAR Global's 100,000+ volunteer
   network; translatewiki's 17,000 translators), and (b) define a *minimum* reviewer-qualification
   floor that doesn't require formal credentials but does require demonstrable native competence +
   independence? Cite: [Localization Lab](https://www.localizationlab.org/),
   [CLEAR Global](https://clearglobal.org/translators-without-borders/),
   [translatewiki languages](https://translatewiki.net/wiki/Translatewiki.net_languages).

3. **"Don't fork/duplicate existing l10n communities" is asserted but not operationalized.** The plan
   says it will "coordinate with" communities but most target projects *already localize through*
   Weblate, Pontoon, translatewiki, or Crowdin/Transifex. The real risk is not forking the project —
   it is **bypassing the project's existing volunteer translator team** by submitting via PR when the
   community expects work in their TMS, stepping on an active translator, or ignoring an established
   per-locale glossary/style guide the team already maintains. Add a registry field for
   *existing-l10n-community* (team contact, active translators for the locale, where they work) and a
   gate: "if an active translator/team exists for this locale, we contribute *through* them / their
   platform and defer to their glossary — we never route around them." This is a sharper, more
   honest version of the consent principle.

4. **MT-provider / "AI-disclosure" coupling.** The plan treats AI disclosure as an open question, but
   incumbents have moved: Crowdin and others now offer Anthropic/OpenAI as first-class MT/AI providers
   and "AI Pipeline" steps to reduce hallucination
   ([Crowdin AI](https://crowdin.com/ai-localization)). Some projects' AI-contribution policies will
   *forbid* AI-assisted strings outright. The disclosure policy should be a **registry-level field
   with three states** (project allows / requires-disclosure / forbids) and a hard gate, not a v0.1
   open question — otherwise `license-000` can pass while the AI-policy fails at submission.

5. **TM/glossary licensing under-specified.** The plan reuses TMX "across projects of the same pair"
   but doesn't state the *license of the TM itself* when source projects have different (even
   copyleft) licenses. A TM aggregating GPL and Apache string pairs is a derivative-rights question.
   Recommend: keep TM entries tagged with source-project provenance + license, and publish the TM
   under the most conservative compatible terms (ties directly to the `translation-memory-commons`
   spin-off below).

6. **"Released in a version that reaches users" may be outside our control.** The Definition of
   Shipped requires upstream *release*, but release cadence is the maintainer's, not ours — a
   correct, reviewed, merged translation could sit unreleased for a year. Keep the bar, but add an
   explicit "delivered (merged) vs shipped (released)" SLA expectation in `partner-001` so a slow
   release schedule doesn't read as program failure. (The plan *names* the delivered/shipped
   distinction — good — but doesn't address the timing-risk to metrics.)

7. **Minor:** Common Voice-style "minimum string %" threshold thinking is missing. Mozilla requires a
   locale to localize a *minimum slice* (recently lowered 824→300 strings) before a language goes
   live ([Common Voice overhaul](https://www.mozillafoundation.org/en/blog/common-voice-localisation-overhaul/)).
   Our "locale completion lift ≥ +25pp / crossing usable bar" metric is good but should define the
   *per-project* "usable threshold" with the maintainer up front, not after the fact.

Overall: the plan is correct, conservative, and gate-rich. The two most important fixes are **(2)
the reviewer-sourcing-for-genuinely-under-served-languages contradiction** and **(3) operationalizing
"don't route around the existing translator community."**

---

## 2. Competitive landscape

This space divides into three layers: **TMS platforms** (where translation happens), **community/NGO
networks** (who translates), and **libraries/QA** (correctness). `localization-for-good` is none of
these — it is an *orchestrated, consent-first contribution program* that rides on top of all three.

**TMS platforms (infrastructure):**

- **Weblate** — GPLv3+, web-based continuous localization with tight Git integration, configurable
  quality checks, translation propagation, and translator credit in VCS; used by 2,500+ libre
  projects, free hosting for libre software, self-hostable via Docker.
  *Strengths:* genuinely open, the default home of many public-good projects, strong built-in checks.
  *Weaknesses:* it is a platform, not a labor force — it doesn't *produce* translations or *source
  reviewers* for under-served languages. ([weblate.org](https://weblate.org/en/),
  [GitHub](https://github.com/WeblateOrg/weblate), [checks](https://docs.weblate.org/en/latest/admin/checks.html))
- **Crowdin / Transifex** — proprietary SaaS, free tier for open source. Now heavily AI-forward:
  Crowdin integrates DeepL/Google/OpenAI/**Anthropic** MT, AI post-editing, and an "AI Pipeline" with
  context-analysis + glossary-check steps to "eliminate hallucinations"; Transifex has an auto-review
  "Quality Index." *Strengths:* polished, AI-native, scale. *Weaknesses:* proprietary; platform terms
  can conflict with open principles (the plan rightly flags this); they automate *drafting*, not
  *trusted native review*. ([Crowdin](https://crowdin.com/ai-localization),
  [Crowdin Dec 2025](https://crowdin.com/blog/whats-new-at-crowdin-december-2025),
  [Transifex/Localization Lab](https://www.transifex.com/blog/how-listening-to-users-in-crisis-built-a-global-platform-for-localization-resilience))
- **Mozilla Pontoon** — open-source TMS behind Mozilla l10n and Common Voice; integrates LLMs in the
  "Machinery" tab for REPHRASE / MAKE FORMAL / MAKE INFORMAL suggestions, explicitly **supportive
  only**, with human localizer review and community approval as the gate. *Strengths:* exemplary
  human-in-the-loop philosophy; proven low-resource onboarding (e.g., Pashto Common Voice via 1,200+
  Pontoon strings). *Weaknesses:* Mozilla-ecosystem-centric; not a general contribution service.
  ([Pontoon LLM blog](https://blog.mozilla.org/l10n/2024/05/28/transforming-translations-how-llms-can-help-improve-mozillas-pontoon/),
  [pontoon.mozilla.org](https://pontoon.mozilla.org/))

**Community / NGO networks (the scarce resource — reviewers):**

- **Localization Lab** — 501(c)(3); 7,000+ volunteers translating 70+ internet-freedom/open-source
  tools into 220 languages; runs "Localization Sprints," culturally-grounded glossary-building, and
  publishes free digital-security glossaries in 48+ languages. *Strengths:* exactly our target domain
  (digital rights/at-risk users), real reviewer network, glossary assets. *Weaknesses:* focused on
  internet-freedom tools; volunteer-capacity-bound. **This is our #1 partnership target.**
  ([localizationlab.org](https://www.localizationlab.org/about-us))
- **Translators without Borders / CLEAR Global** — 100,000+ volunteer language community; specializes
  in life-saving humanitarian info and **building MT/data capacity for under-resourced languages**;
  publishes humanitarian language datasets on HDX. *Strengths:* unmatched under-served-language reach,
  humanitarian credibility. *Weaknesses:* humanitarian-content-focused (not primarily software UI);
  enterprise/NGO-funded model. ([clearglobal.org](https://clearglobal.org/translators-without-borders/),
  [HDX](https://data.humdata.org/organization/clear))
- **translatewiki.net** — Translate-extension wiki; 17,000 translators, 120,000+ messages, 50+
  projects (MediaWiki, OpenStreetMap, MantisBT…); 13%-coverage export threshold; twice-weekly VCS
  export. *Strengths:* huge volunteer base, low barrier, proven software-UI focus.
  ([translatewiki.net](https://translatewiki.net/wiki/Translating:MediaWiki),
  [Wikipedia](https://en.wikipedia.org/wiki/Translatewiki.net))
- **KDE / GNOME language teams** — mature volunteer per-language teams; KDE in 65+ languages (Scripty
  automation + Lokalize), GNOME in 37+ (gettext PO, GNOME Translation Editor). *Strengths:* gold
  standard for sustained quality + glossary/style-guide governance. *Weaknesses:* tied to their own
  ecosystems; would view drive-by outside contributions skeptically — reinforcing our consent-first
  thesis. ([l10n.kde.org](https://l10n.kde.org/), [techbase KDE](https://techbase.kde.org/Localization))

**Libraries / QA (correctness — what our lint should reuse, not reinvent):**

- **Translate Toolkit** — Python library; `pofilter` runs 40+ checks (variables, accelerators,
  capitalization, XLIFF/TMX support) and `pomerge` reintegrates fixes; the foundational format/QA
  layer beneath Weblate. *Implication:* our i18n-lint should wrap/reuse this where possible rather
  than hand-rolling every check. ([PyPI](https://pypi.org/project/translate-toolkit/),
  [pofilter](http://docs.translatehouse.org/projects/translate-toolkit/en/latest/commands/pofilter.html))
- **Unicode CLDR** — plural-rule source of truth for the plural-completeness check (plan already
  cites it).

**Key takeaway:** No incumbent does what this project does. Platforms supply *where*; communities
supply *who*; libraries supply *correctness checks*. Nobody offers an **AI-accelerated,
consent-first, native-reviewed contribution pipeline targeted at neglected public-good projects in
under-served languages**. That white space is real — *provided* we partner for reviewers rather than
compete with the communities that hold them.

---

## 3. Gaps we can fill

1. **The "small neglected public-good project" gap.** Localization Lab serves internet-freedom tools;
   CLEAR Global serves humanitarian content; KDE/GNOME/Mozilla serve their own ecosystems. The long
   tail — a civic-participation tool, an open-education app, an accessibility utility, a HOT/OSM
   adjacent tool — that has i18n wiring but *no localization community and no l10n capacity* is
   precisely who falls through every existing net. That is our beneficiary.
2. **AI-accelerated drafting that incumbents distrust by default.** Communities reject AI because it
   arrives as unreviewed slop. We can supply the *missing governance wrapper* (lint-before-human +
   mandatory native review + provenance) that makes AI drafting *acceptable* to those same
   communities — turning a liability into accelerated throughput.
3. **Correctness guarantees as a first-class deliverable.** The DevOps field report's core finding —
   maintainers "have no practical way to evaluate" a translation PR — is a gap we close: every
   deliverable arrives lint-clean (placeholder/plural/markup verified) *and* with a recorded
   independent native sign-off, so the maintainer's review cost drops toward zero.
4. **Reusable cross-project terminology + TM commons** for under-served pairs, which today are
   fragmented per-project or locked behind paywalls (a problem CLEAR Global explicitly names about
   language data).
5. **Staleness/re-sync as a maintained service.** The plan rightly calls drift the dominant long-term
   cost; most volunteer efforts let locales rot after launch. A source-sync watcher + re-sync cadence
   is a durable differentiator.

---

## 4. Differentiators to win

1. **Consent-first identity as a brand, not a feature.** In a market where maintainers are installing
   "kill switches" against AI PRs, "we only contribute where invited, through your process, lint-clean
   and natively reviewed" is a *trust* position no AI-translation vendor can credibly claim.
2. **Lint-before-human hard gate** — reviewer time is never spent on mechanically broken output;
   correctness is provable, not asserted.
3. **Independent native review + provenance + AI-disclosure** baked into every artifact — addresses
   the exact maintainer pain point ("can't evaluate the PR").
4. **Partner-with-not-compete posture** toward Localization Lab / CLEAR Global / translatewiki — we
   bring AI throughput + correctness tooling + governance; they bring reviewers + legitimacy. Symbiosis,
   not displacement.
5. **Outcome metric = merged AND released to users**, not PRs opened — structurally immune to the
   vanity metrics that produce slop.

---

## 5. Claude API leverage — and the hard limits

**Where Claude adds leverage (drafting + tooling assistance only):**

- **MT drafts + post-editing.** Claude produces a *draft* translation preserving every
  placeholder/ICU/markup token, applying glossary + TM + the project's style guide (register/formality).
  Mozilla's finding that "output quality heavily depends on how well the input is framed" argues for a
  Crowdin-style **multi-step pipeline** (context analysis → draft → glossary check → self-critique)
  rather than one-shot translation. Anthropic is already a supported MT provider in Crowdin, so this is
  proven viable.
- **Glossary + TM bootstrapping.** Claude proposes candidate term pairs, do-not-translate lists,
  register tags, and TMX seeds from the source catalog + any existing translations — accelerating the
  ≥30-term M0 glossary goal — for **human ratification**.
- **Placeholder / ICU / plural validation assistance.** Claude can *explain* and *localize* a lint
  failure (e.g., "Polish needs `few`/`many` categories; current string only has `one`/`other`"), and
  generate known-bad/known-good test fixtures for the lint suite. (The *gate* itself stays
  deterministic code wrapping Translate Toolkit/CLDR — not Claude's judgment.)
- **Context notes for reviewers.** Claude drafts per-string context, ambiguity notes, and
  `UNCERTAIN:` flags (term/plural/placeholder/register/cultural/ambiguous-source) to focus scarce
  reviewer attention — a force-multiplier on the binding constraint.
- **Back-translation QA drafts** for safety-domain strings (the human expert still adjudicates).
- **Registry/eligibility research assistance** — drafting candidate-project assessments against the
  4-criterion rubric for human verification.

**Where Claude must NOT decide (blocking, human-only):**

- **Raw/unedited MT is never shipped** — Claude output is always a draft pending human review.
- **Native-speaker review is blocking and human** — independent of the drafting agent; Claude cannot
  sign off, cannot self-approve, cannot resolve its own `UNCERTAIN:` flags.
- **Cultural adaptation, register/formality (T-V), gender, and non-partisan civic framing** are human
  judgments.
- **Maintainer coordination / consent** — a human secures and records consent; Claude never initiates
  contributions or "decides" a project welcomes them.
- **License + CLA/DCO + AI-policy verification** — human/compliance-reviewer determination; Claude may
  surface evidence, never certify eligibility.
- **Quality acceptance / Definition of Shipped** — merged-and-released is confirmed by humans against
  upstream commits/releases, not asserted by the model.

This mirrors Mozilla's explicit line: LLMs "support, not replace" experienced localizers.

---

## 6. Ten concrete optimizations

1. **Wrap Translate Toolkit `pofilter` + CLDR** in the i18n-lint instead of hand-rolling 40+ checks —
   faster to ≥3/≥5 format coverage and battle-tested. Keep the agent-neutral wrapper; reuse the engine.
2. **Add a bidi/RTL string-correctness lint rule** (LRM/RLM/FSI/PDI isolation around placeholders) and
   state explicitly that string-level bidi is in-scope while CSS/layout is not — closes the gap in §1.
3. **Add registry field `existingL10nCommunity`** (team/platform/active-translator-for-locale) plus a
   hard gate: contribute *through* the existing team/platform, defer to their glossary, never route
   around an active translator. Operationalizes "don't fork/duplicate."
4. **Promote AI-disclosure from open question to a registry tri-state gate** (allows / requires-disclosure
   / forbids), checked in `contrib-002` so AI-policy failures surface at `license-000`, not at submit.
5. **Define a reviewer-qualification *floor* for thin-pool languages** (native competence + independence
   + community vouching) so genuinely under-served locales aren't structurally excluded by a
   credential bar — resolves the mission-drift contradiction.
6. **Sign a reviewer-network MOU early** with Localization Lab and/or CLEAR Global as the M1
   reviewer-sourcing path, rather than recruiting individuals cold — they already hold 220-language and
   100k-volunteer networks respectively.
7. **Adopt a Crowdin-style multi-step Claude drafting pipeline** (context → draft → glossary/placeholder
   self-check → uncertainty flags) to cut reviewer rework, per Mozilla's prompt-framing finding.
8. **Set the per-project "usable threshold" with the maintainer up front** (Common-Voice-style minimum
   slice) so "completion lift / crossing usable bar" is defined, not retrofitted.
9. **Tag every TM entry with source-project provenance + license** and publish the TM commons under the
   most conservative compatible terms — pre-empts the copyleft-mixing risk and feeds the TM-commons
   spin-off.
10. **Add a delivered-vs-shipped SLA note + release-timing expectation** to `partner-001`/metrics so a
    slow upstream release cadence doesn't read as program failure (track "merged" and "released"
    separately; report both).

---

## 7. Parallel & perpendicular spin-offs

- **`translation-memory-commons` (parallel).** The per-pair TMX this project builds is reusable across
  the whole portfolio. Spin it into an open, provenance-tagged, license-aware TM/glossary commons for
  under-served pairs — directly addressing CLEAR Global's "language data locked behind paywalls"
  complaint. This project is the first contributor and first consumer.
- **`public-domain-translations` (perpendicular).** Same review/lint/provenance discipline applied to
  public-domain *text* (not software UI) — books, civic documents. Shares the reviewer network and
  glossary tooling; different content-licensing model (CC/PD vs derivative-of-upstream).
- **`vital-info-translations` (parallel, already a named sibling).** Humanitarian/health/safety info
  in under-served languages — overlaps CLEAR Global's core mission. Shares the high-risk-tier
  expert-review + verbatim-disclaimer machinery; this project should reuse that sibling's review
  discipline (PLAN already references it).
- **`onco-research-software-docs` (perpendicular).** High-stakes medical software docs/UI — exercises
  the `riskDomain≠none` path (second reviewer + back-translation + credentialed expert sign-off).
  Validates the high-tier gates that `localization-for-good` defines but rarely triggers.
- **Reusable l10n-contribution pipeline (the core asset).** The intake → lint → glossary/TM → draft →
  uncertainty-flag → native-review → contribution-compliance → merge/release flow is a portable
  product. Package it as a reusable Elyos donated-lane pipeline that any of the above projects invoke.
- **MCP server (`l10n-tools` MCP).** Expose the i18n-lint (placeholder/ICU/plural/bidi parity),
  glossary/TM lookup, CLDR plural-rule queries, and registry-eligibility checks as **deterministic
  MCP tools** an agent can call during drafting — keeping the *gates* in code while letting Claude
  consult them. This is the cleanest agent-neutral way to give the drafting agent correctness feedback
  without letting it self-certify. Strong candidate to build once and share across all translation
  spin-offs.

---

## 8. Open questions

1. **Reviewer sourcing for zero-pool languages** — concretely, what is the floor qualification and the
   community-vouching mechanism when *no* professional reviewer exists for a target locale? (This is
   the program's true binding constraint; §1.2 / opt-5/6.)
2. **Partnership vs. independence** — do we formally MOU with Localization Lab / CLEAR Global /
   translatewiki (faster reviewers + legitimacy, but their priorities steer ours), or stay independent
   (slower, but free to pick neglected projects)? Recommend partner-led for M1.
3. **AI-disclosure norms** — how do we handle projects that *forbid* AI-assisted contributions
   entirely? Hard-exclude, or offer a human-only drafting mode? (Affects addressable market size.)
4. **TM commons licensing** — what single license can lawfully cover TM entries aggregated from GPL +
   Apache + MPL source projects? Needs a legal determination before the TM is published.
5. **Where is the line on "context/short in-app help"** vs. UI strings? Some tooltips/onboarding blur
   the UI-vs-docs boundary the plan draws.
6. **Funded-lane surge** — is there ever a case for metered Claude drafting under escrow (hard
   per-task cap) when a humanitarian deadline demands volume the donated lane can't meet? (Explicitly
   out of v0.1, but the §3 humanitarian gap will create demand.)
7. **Metric attribution for release timing** — if a translation is merged but the maintainer doesn't
   cut a release for months, how is that represented so the program isn't penalized for an upstream
   decision outside its control?

---

### Sources

- AI-PR maintainer burden: [The New Stack](https://thenewstack.io/ai-generated-code-crisis/) ·
  [The Register](https://www.theregister.com/2026/02/03/github_kill_switch_pull_requests_ai/) ·
  [InfoWorld](https://www.infoworld.com/article/4127156/github-eyes-restrictions-on-pull-requests-to-rein-in-ai-based-code-deluge-on-maintainers.html) ·
  [DevOps.com l10n field report](https://devops.com/what-five-localization-pull-requests-revealed-about-open-source-governance-a-field-report-on-open-sources-i18n-infrastructure-gap/)
- Weblate: [weblate.org](https://weblate.org/en/) · [GitHub](https://github.com/WeblateOrg/weblate) ·
  [checks](https://docs.weblate.org/en/latest/admin/checks.html)
- Crowdin/Transifex: [Crowdin AI](https://crowdin.com/ai-localization) ·
  [Crowdin Dec 2025](https://crowdin.com/blog/whats-new-at-crowdin-december-2025) ·
  [Transifex/Localization Lab](https://www.transifex.com/blog/how-listening-to-users-in-crisis-built-a-global-platform-for-localization-resilience)
- Mozilla Pontoon / Common Voice: [Pontoon LLM blog](https://blog.mozilla.org/l10n/2024/05/28/transforming-translations-how-llms-can-help-improve-mozillas-pontoon/) ·
  [Common Voice l10n overhaul](https://www.mozillafoundation.org/en/blog/common-voice-localisation-overhaul/) ·
  [pontoon.mozilla.org](https://pontoon.mozilla.org/)
- Localization Lab: [about](https://www.localizationlab.org/about-us) · [home](https://www.localizationlab.org/)
- CLEAR Global / TWB: [clearglobal.org](https://clearglobal.org/translators-without-borders/) ·
  [HDX datasets](https://data.humdata.org/organization/clear)
- translatewiki: [Translating MediaWiki](https://translatewiki.net/wiki/Translating:MediaWiki) ·
  [Wikipedia](https://en.wikipedia.org/wiki/Translatewiki.net) ·
  [languages](https://translatewiki.net/wiki/Translatewiki.net_languages)
- KDE/GNOME: [l10n.kde.org](https://l10n.kde.org/) · [KDE TechBase](https://techbase.kde.org/Localization)
- Translate Toolkit: [PyPI](https://pypi.org/project/translate-toolkit/) ·
  [pofilter](http://docs.translatehouse.org/projects/translate-toolkit/en/latest/commands/pofilter.html)
