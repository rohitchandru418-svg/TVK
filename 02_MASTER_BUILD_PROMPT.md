# MASTER BUILD PROMPT: Naadi (Narrative Operations Room), Phase 1

> Paste this whole document into your coding agent / engineering lead as the starting brief. Companion files: `01_Concept_and_Ideation.md`, `03_Detection_and_Scoring_Spec.md`, `04_Architecture_Data_Model_ADRs.md`, `05_UX_and_Design_Spec.md`, `06_Roadmap_Evals_and_PRD_Gaps.md`, and the source PRD ("TVK War Room Social Listening, Phase 1 (X + News)", 2026-11-03).

---

## 0. Your role and how to work

You are the lead engineer and product-minded architect building **Naadi**, a production system for the TVK war room. You own delivery end to end: architecture decisions, code, tests, evals, observability, and docs.

**Working agreements**
1. **Read the PRD and all companion files first.** Where they conflict, the PRD wins; where the PRD is silent or ambiguous, use the companion specs and record the choice as an ADR. Never silently invent a requirement.
2. **Before writing code, produce ADRs** for each open decision in the PRD ("Open decisions for CTO") and in `04_Architecture_Data_Model_ADRs.md`, each with options, a recommendation, cost and user impact, and what would change your mind. Ask the human only about decisions that are hard to reverse (X contract, retention rules, presentation-safe defaults, attribution rules). Decide the rest and flag them.
3. **Build in vertical slices** (section 11). Each slice ships working end to end behind a feature flag, with tests, telemetry and a short demo script. Do not build layers in isolation.
4. **Everything client-specific is data, not code.** (Section 3, principle 6.) If you type "TVK", "Vijay", "ally" or "CM" in application code, stop and move it to a Doctrine Pack.
5. **Measure, never assert.** Every accuracy, freshness or cost claim must come from an automated measurement with a stored result.
6. **Small, reviewable commits.** Keep a `DECISIONS.md` log and a `STATUS.md` listing what works, what is stubbed, and what is blocked on the human.
7. If something is blocked (X contract, e-paper terms, WhatsApp template approval), build against an **adapter interface with a fake**, ship the slice, and list the blocker.

---

## 1. Mission

Give the TVK war room **one live, trustworthy picture** of how X and the news talk about TVK, CM Vijay and Minister AadhavArjuna, plus every other Indian party (allies watched as closely as opposition), with an AI co-worker that explains it and alerts the right person **in time to act**. Freshness target: **5 minutes or less from post to screen.**

The product's centre of gravity is **timing, not sentiment**. It must answer: *What is happening? Does it matter, and is it real? Do we need to move, and how long do we have?*

**Phase 1 does not post, reply, campaign or publish. The co-worker must have no tool capable of it.**

---

## 2. Users and surfaces

| User | Surface | Needs |
|---|---|---|
| War-room media, social, content teams | **Desk** (web app, Tamil/English) | Story board, drill-down, acknowledge/assign, correct labels, ask the co-worker |
| War-room leads (incl. **Sajan**: response opportunities) | Desk + **WhatsApp** | Alerts, response queue, confirm attribution, review corrections |
| Whole room, visitors | **Wall** (big screen) | Glanceable sentiment, max 3 alerts, Pulse Strip, presentation-safe mode |
| **CM Vijay** | **CM Card** | 30-second summary: ≤3 changes, cause in one line, tap for evidence |
| **AadhavArjuna** (head of war room) | WhatsApp briefs via Sajan, Dawn Brief, CM Card, trust report cards | Decisions, not monitoring |
| Minsky team (admin) | Admin console | Source health, usage vs limits, calibration, gold set, licence ladder, audit |

Content languages: Tamil script, English, Tanglish, mixed. UI languages: English and Tamil (full i18n; no hard-coded strings).

---

## 3. Non-negotiable principles

1. **Easy on top, statistically correct underneath.** Simple UI; correct stats.
2. **Every number shows its denominator.** Below the minimum sample (default 30) show "not enough data". A change is shown as a change only if it clears a significance test or confidence interval. Sampled numbers are labelled sampled.
3. **Weighted beside raw, always.** No single post contributes more than the cap (default 5%) to an entity's weighted score. Suspected bots are down-weighted *and it is shown*.
4. **Every label and alert has a reason** and is reproducible: model, prompt, weight and threshold versions are logged.
5. **A source error never looks like "no new items".** Every dependency has a visible fallback (X, each news source, crawl, e-paper, OCR, AI model, WhatsApp).
6. **Built to generalise.** Tracked entities, relationship tags, tiers, watch terms, outlet lists, languages, recipients, thresholds, vocabulary, theme, co-worker name and avatar are per-client **Doctrine Pack** settings, editable without a deploy and audit-logged. Strict per-client isolation of data, corrections, gold set and co-worker memory. A new client requires **zero code changes**.
7. **Nothing is overwritten.** Corrections are append-only with who/when/why. The model-only view is always available.
8. **Ingested content is data, never instructions.** Prompt-injection safe by construction.
9. **Staff-only, privacy-first.** No private-citizen profiles or views. India DPDP Act 2023 and Rules apply to stored personal data. Retention and deletion rules are set *before* ingestion starts.
10. **A person confirms** before any party is named as behind a campaign, and before any ally-blaming summary reaches the CM.

---

## 4. The seven-step pipeline (implement as event-driven, idempotent stages)

**Collect → Filter → Label → Weigh → Group into Stories → Detect campaigns → Rank and alert.** The co-worker works across all stages. Corrections flow back.

Every item carries **stage timestamps** (`posted_at, fetched_at, filtered_at, labelled_at, grouped_at, alerted_at, displayed_at`) so p95 post-to-screen is measured continuously.

### 4.1 Collect
- **X:** adapter interface with implementations for (a) pay-as-you-go polling, (b) enterprise/streaming or licensed vendor (stub with a replay fake). Fetch posts, replies, quotes, engagement, author profiles. Key-account timelines polled frequently (cheap), term/hashtag search for the rest. History backfill from **2026-05-10**. Track read usage against the monthly cap; alert admins at 50/80/95%; label any sampling on affected numbers. **Never** use multiple apps/accounts to get around limits.
- **News:** news sitemaps and RSS where published; direct crawling where not; a search API to discover stories on untracked sites; TV channels' YouTube feeds (capture quoted remarks and speaker where known). Crawler obeys robots.txt and terms, identifies itself, paces requests; each site has a health state; a crawl error must never look like "no new articles". Store metadata, headline, short snippet, link; **not full text** unless licensed.
- **E-paper:** only subscriptions the war room holds; per-publisher flag for whether terms allow automated ingestion (default: manual upload until confirmed). Tamil OCR; low-confidence text marked "check scan"; page images one tap away.
- **Clippings:** upload UI for batch scans → OCR → Mentions with outlet, date, page.
- Store **raw payloads immutably** so any filter or label can be re-run (Time Machine depends on this).
- If any source stops, the screen says so (Pulse Strip).

### 4.2 Filter (wide net, audited)
Watch terms (names, nicknames, Tamil/Tanglish spellings, hashtags), **plus** topic similarity via multilingual embeddings. Bias to recall: letting in extra is fine, missing is not. Weekly audit samples what was filtered *out* (stratified by source, language, entity) and publishes the missed-item rate. The word "Vijay" must not match film chatter blindly: use entity disambiguation context (politics vs cinema) and show the reason.

### 4.3 Label (cheap first, strong when needed)
Per item: entities concerned; **polarity** per entity (positive/negative/neutral/mixed) **and a separate sarcasm flag**; attack yes/no; claim backing (on record / named source / anonymous insinuation / satire); language (own detector; do not trust X's tag for Tanglish); a one-line **reason**; confidence. Cheap classifier for every item; uncertain items escalate to the strong model, then to a human queue. Tamil numerals and amounts must be read. Targets (defaults, set in calibration): F1 0.80 English, 0.75 Tamil, 0.72 Tanglish.

### 4.3.1 Cost discipline
Cheap counts before expensive reads; no item paid for twice (content-hash cache); cheap classification for every item; strong model only for reasoning, summaries, Q&A; batch mode for backfill. No monthly cap, but log cost per item, per stage, per client.

### 4.4 Weigh
Influence score per account/outlet from verified/notable status, log-scaled followers, engagement velocity and a staff-maintained standing tier. Treat X's paid verification as **weak**; use a staff-confirmed "notable" flag. Outlet weights carry a visible reason ("party-affiliated channel", "state daily, Tamil, high reach"). All weights versioned, editable, audit-logged. See `03_Detection_and_Scoring_Spec.md`.

### 4.5 Group into Stories
Fuse items about the same happening across X, news, TV, e-paper, clippings. Reach split by source type ("9 outlets, 1,200 posts"). Single-source **anonymous insinuations** surface as a Story of one marked "unattributed". Staff can merge/split; merges are logged and teach the clusterer. Compute **Origin Trace** (first-seen item and propagation tree).

### 4.6 Detect campaigns
Signals: young/low-history accounts clustering, near-identical text, synchronised timing, mutual boosting, hashtag pushed by a small network. Output a confidence level and an evidence pack (accounts, ages, shared phrasing, timing chart, **organic-ness index**). Language stays at "possible coordination" until a named lead confirms attribution (logged with name and reason). Track a confirmed campaign's spread over time next to our response Stories.

### 4.7 Rank and alert
Severity = influence × negativity × velocity (see spec for the full formula, floors and tiers). Top tier: flash on the wall and WhatsApp to team leads. Next tier: in-app. Wall cap: **3 alerts** (default); related items grouped into one alert. Alert types: volume spike, influence trigger, campaign, ally tremor, **response opportunity**, source-health. Anyone can acknowledge/assign; the flash clears for everyone with "taken by <name>". WhatsApp: retry on failure; in-app shows "WhatsApp not delivered" and notifies a lead.

---

## 5. Signature capabilities (what makes Naadi unique; build these)

1. **Mileage Clock**: each Story has a response window (Open / Closing / Closed) with a predicted-peak band and an "early estimate" label when data is thin.
2. **Reaction Roster**: per Story, expected reactors shown as spoken / silent / not yet, including **TVK's own response status** read from TVK leaders' accounts. Silence is a first-class signal.
3. **Response Opportunity engine** (stories 31, 32, 33): WhatsApp to Sajan within 5 minutes of trigger when an attack on a tracked entity draws condemnation and no TVK leader has responded; shows the clip/article, who is already reacting, and "no TVK response yet"; stops alerting when TVK responds. Response queue with suggested kind (condemn, rebut, congratulate, condole); acted/not-needed choices teach the queue. Also supports opportunities that are positive (congratulate, claim credit).
4. **Coalition Seismograph + Ally Mood panel**: ally tremor detection against each ally figure's own baseline, per issue.
5. **Constellation** campaign view with organic-ness index and attribution gate.
6. **Origin Trace** (Patient Zero).
7. **Tone Lab**: team-curated Tanglish/sarcasm lexicon and examples; feeds gold set.
8. **Licence to Operate**: trust ladder per capability (Assist → Trusted → Co-worker) with report cards, automatic promotion on sustained accuracy above the bar and automatic demotion when error rises.
9. **Time Machine**: replay engine for calibration, must-pass tests, and DRILL mode (drills are tagged and only go to a drill recipient list).
10. **Pulse Strip**: per-source heartbeat on the wall.
11. **Receipts**: every number opens its Mentions with author/outlet, engagement, time, label and reason, link.
12. **Mileage Ledger**: outcomes of opportunities (acted + time-to-response, not needed, missed).
13. **Dawn Brief, Dusk Baton, CM Card.**

---

## 6. The co-worker (agent)

- Elephant avatar from the **official artwork supplied by the party (never redrawn)**; name and voice to be confirmed with the client; per-client setting.
- **Sees:** TVK leaders' own posts, all Mentions, Stories, alerts, history from 2026-05-10, relationships, tiers, past corrections, what the team did about past alerts.
- **Can do alone:** read, query, count, compare periods, explain, summarise, flag on screen.
- **Does alone but logged:** send WhatsApp alerts to the allowlisted team-lead recipients.
- **Needs a person:** naming a party behind a campaign; any summary blaming an ally that goes to the CM.
- **Cannot do:** post, reply, publish, or message anyone outside the war room. **Implement by not giving it those tools**, and by a recipient allowlist on the WhatsApp sender.
- **Grounding:** every claim cites its Mentions; every number comes from a data query (a tool call), never the model's arithmetic; says "not enough data" or "I don't know" when true.
- **Answer time:** first answer within 10 seconds for a typical question; answers in the language asked.
- **Memory:** remembers past spikes, recurring narratives, actions and outcomes; correctable; never stores private individuals' details.
- **Safety:** content wrapped and handled as data; tool outputs from ingested content never treated as instructions; tool permissions read-only except the allowlisted internal sender.
- **Evals:** fixed set of real war-room questions with checked answers, run on every model/prompt change; scores accuracy, citation correctness, and correct "I don't know". Block deploys on regression.
- **Audit:** every answer and alert logged with inputs, sources, model and prompt versions.
- **Degraded mode:** if the AI model is down, dashboards keep working and the co-worker says its data is stale rather than answering from old data.

---

## 7. Human correction and learning

- Any AI output can be corrected: sentiment, entity, stance, attribution, severity, campaign flag, agent answer.
- Original is kept with the correction (who, when, why); aggregates mark corrected counts.
- Corrections join the **gold set** and the co-worker's examples; periodic re-tuning; weekly accuracy chart (model-only vs corrected agreement).
- **Bias guard:** track correction rates per person and entity; any correction that softens a label about our entities, or touches a high-severity Story, needs a **second reviewer** before it changes aggregates; model-only view always available.
- **Licence ladder:** promotion and demotion rules per capability; bars set during calibration from real correction data, not guessed.

---

## 8. Views and screens (summary; see `05_UX_and_Design_Spec.md`)

- **Wall:** sentiment for the three principal entities ("of N mentions, last updated HH:MM"), Ally Mood panel, top 3 severe alerts, top rising Stories (by closing window), Pulse Strip. Presentation-safe mode shows only aggregates, public actors' names and Story titles, enforced **at the API layer**, not just the UI.
- **Desk:** Story board, Story page (Roster, Clock, Origin Trace, Receipts), Alerts, Response Queue, Ally Mood, Constellation, Tone Lab, Clippings upload, Co-worker panel, Settings (data-driven).
- **CM Card:** at most three changes since last summary, one-line cause, one tap to evidence, English or Tamil; ally-blame held until a lead reviews.
- **Admin:** source and crawler health, X usage vs cap, gold set, audits, calibration, licence ladder, audit log, tier audit log.

---

## 9. Technical constraints (summary of PRD "AI/Tech Constraints")

- **Freshness:** X post-to-screen and to alert ≤5 min (client target, measure p95); news as fast as the feed allows (state the floor per source type); e-paper and clippings the same morning.
- **Volume:** unknown; measure in the first two weeks.
- **X:** pay-as-you-go caps at 3M post reads per month (about 100k per day). Tracking all parties, campaign detection and backfill will exceed it; the likely route is enterprise or a licensed vendor. Check X display rules for broadcast/wall use of post text before launch.
- **Statistics:** log-scaled followers, per-post cap, versioned documented weights, significance tests for changes.
- **Reproducibility:** all settings are data rows, changeable without deploy, audit-logged.
- **Failure behaviour:** designed fallback and visible state for every third-party dependency.

---

## 10. Suggested stack (defaults; override via ADR)

- **Monorepo, TypeScript** for web and API (Next.js/React, tRPC or REST + OpenAPI), **Python** workers for crawling, NLP, OCR and detectors.
- **Postgres** (row-level security per tenant, pgvector) as system of record; **time-series store** (TimescaleDB or ClickHouse) for counts, velocity and baselines; **object store** for raw payloads, page images, scans.
- **Queue/stream:** Redis Streams or NATS to start; Kafka only if the firehose requires it.
- **Models:** tiered. Small/cheap model or fine-tuned classifier for every item; strong model for reasoning, summaries, Q&A; multilingual embeddings (e.g., bge-m3 or multilingual-e5 class) for filter and Story clustering. Evaluate per-language models vs one model on gold-set results.
- **Crawling:** feed-first; generic article extraction by default; site-specific rules for key outlets; headless browser only where required.
- **OCR:** evaluate build vs buy for Tamil on real pages; pick on measured accuracy.
- **WhatsApp:** official Business API with approved utility templates; individual recipients (not groups); delivery receipts logged. Quiet-hours and escalation rules per recipient.
- **Observability:** OpenTelemetry traces, stage-latency histograms, per-source health, cost per item, alert precision dashboards.
- **i18n:** ICU message format; Tamil-capable fonts (Noto Sans Tamil / Anek Tamil class); Tamil numerals handled in data and UI.

---

## 11. Build order (vertical slices, each with exit criteria; details in `06_*`)

- **S0 Foundations:** tenancy and Doctrine Pack loader, config-as-data with audit, auth/RBAC, i18n shell, stage-timestamp telemetry, Pulse Strip skeleton.
- **S1 X ingest + filter:** adapter + fakes, raw store, wide filter, usage meter, backfill tool.
- **S2 Label + Tone Lab + corrections:** schema (polarity + sarcasm split), cheap/strong routing, reasons, correction UI and bias guard, gold-set tooling.
- **S3 Weigh + Wall v1 + Receipts:** influence scoring, weighted-beside-raw, "of N" and "not enough data", presentation-safe mode.
- **S4 Alerts (must-pass #2):** severity, influence-floor path, big-screen flash, WhatsApp, ack/assign, delivery failure path.
- **S5 News + Stories:** feeds, crawl, YouTube, clustering, reach split, merge/split, Origin Trace, crawl health.
- **S6 Co-worker v1:** grounded Q&A with data tools, citations, evals, Tamil/English, degraded mode.
- **S7 Spike + Ally Seismograph:** baselines, event calendar, tremor detection, Ally Mood panel.
- **S8 Response Opportunity + Roster + Mileage Clock (must-pass #1) + Time Machine.**
- **S9 Constellation:** campaign detection, evidence pack, attribution gate, spread tracking.
- **S10 E-paper + clippings + Tamil OCR.**
- **S11 Licence ladder + Ledger + Dawn Brief/Dusk Baton/CM Card.**
- **S12 Hardening:** load and failure drills, security review, DPDP retention, display-rule check, calibration with live data, launch.

---

## 12. Definition of done (for the release)

1. **Must-pass 1 (councillor incident)** and **must-pass 2 (one post that mattered)** pass as automated replay tests, with latency assertions (≤5 min), on every release.
2. p95 post-to-screen ≤5 minutes on X on normal days; spike-day behaviour documented and load-tested.
3. Per-language F1 meets the agreed bars on the gold set (defaults 0.80 / 0.75 / 0.72), or the capability stays on Assist.
4. Every number opens receipts; every label has a reason; every percentage shows its denominator.
5. Source failure drills: kill X, a news source, OCR, the model and WhatsApp; each shows a visible degraded state; none looks like "no new items".
6. Presentation-safe mode verified by automated tests at the API layer.
7. Co-worker eval suite passes thresholds; prompt-injection test suite passes.
8. A second dummy client (a business Doctrine Pack) loads with **no code change** and is fully isolated.
9. Retention/deletion rules active; DPDP review done; audit logs complete.
10. Admin page shows source health, X usage, licence ladder, missed-item rate, correction rates.

---

## 13. First 48 hours: do this

1. Read the PRD and all companions; write a half-page summary of how you understand the product and list every ambiguity.
2. Draft ADR-001 to ADR-012 (see file 04), each with a recommendation.
3. Define the Doctrine Pack JSON schema and write the TVK pack and a fictional business pack.
4. Create the replay fixtures for the two must-pass scenarios (see file 03, section 9).
5. Stand up S0 skeleton with stage-timestamp telemetry and a fake X adapter producing the fixtures end to end to a placeholder wall.
6. Send the human the open-questions list (file 06), grouped by what blocks what.
