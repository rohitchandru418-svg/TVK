# Architecture, Data Model and ADR Candidates

This is an **input to the CTO**, not a final design. It proposes defaults and lists decisions to confirm.

---

## 1. System shape

```
 SOURCES                  INGEST            CORE PIPELINE (event-driven, idempotent)                 SURFACES
 X (poll/stream/vendor) ─┐                                                                         ┌─ Wall (big screen)
 News feeds / sitemaps ──┤                  filter ─► label ─► weigh ─► story ─► detectors ─► alert ─┤─ Desk (web)
 Site crawlers ──────────┼─► adapters ─► raw  (embeddings)  (cheap→strong→human)  (clustering) (spike│─ WhatsApp
 YouTube channel feeds ──┤   (fakes for   store                                    +origin  ally│    └─ CM Card
 E-paper (subscribed) ───┤    tests)      (immutable)                                      campaign│
 Clipping uploads + OCR ─┘                                                          response opp.)  │
                              ▲                                                                       │
                              └── corrections / gold set / licence ladder / Time Machine (replay) ◄───┘
 CO-WORKER (agent): read-only data tools + allowlisted internal WhatsApp sender; memory; evals
 CROSS-CUTTING: tenancy + Doctrine Packs, config-as-data, audit log, telemetry, source health (Pulse)
```

**Latency budget for the 5-minute X target (p95):** fetch ≤90 s, filter ≤15 s, label ≤45 s, story+detectors ≤45 s, alert+deliver ≤45 s, display ≤15 s = ~4 min 15 s, leaving about 45 s margin. Stage timestamps on every item make the budget measurable. If streaming access is not secured, polling cadence on key accounts and rule-based search must be tuned to fit; document the actual floor.

---

## 2. ADR candidates (write each with options, recommendation, cost, user impact, reversal trigger)

| # | Decision | Recommendation (default) |
|---|---|---|
| ADR-001 | How X data is obtained | Start enterprise/licensed vendor talks now. Build an adapter; run pay-as-you-go for dev and key-account timelines until the contract lands. Never multi-account. |
| ADR-002 | X fetch speed | Streaming where available; otherwise tiered polling (key accounts every 30-60 s, term search every 60-120 s, events tightened by calendar). |
| ADR-003 | News crawling | Feed-first; generic extractor default; site rules for ~15 key outlets; headless browser only where required; per-site health and robots/terms compliance. |
| ADR-004 | Label models | Start with one cheap classifier + strong-model escalation; decide per-language models on gold-set F1 and cost. |
| ADR-005 | Tamil OCR | Bake-off on real e-paper pages and clippings (accuracy on numerals included); choose build or buy. |
| ADR-006 | Story merging | Embedding clustering + entity/time constraints; staff merge/split; reach computed per source type, de-duplicated. |
| ADR-007 | Agent memory | Structured memory store (facts with provenance and edit history) + retrieval; corrections update memory immediately. |
| ADR-008 | Tenancy | Single deployment, row-level security by `tenant_id`, per-tenant keys, per-tenant vector namespace; option to move large tenants to isolated stacks. |
| ADR-009 | Time-series store | TimescaleDB/ClickHouse for counts; Postgres for entities. |
| ADR-010 | WhatsApp delivery | Official Business API, approved utility templates (Tamil + English), individuals not groups, delivery receipts, retry + in-app fallback. |
| ADR-011 | Retention and DPDP | Raw payload retention window, minimisation (store metadata and snippet), deletion workflow, consent/lawful basis review; decided *before* ingestion starts. |
| ADR-012 | Presentation-safe enforcement | A server-side response filter by view mode; automated tests; never UI-only. |
| ADR-013 | Cost routing | Content-hash cache, cheap-first routing, batch backfill, per-stage cost telemetry. |
| ADR-014 | Replay architecture | Pipeline reads from the raw store through the same code path with a virtual clock; outputs to a sandbox namespace. |

---

## 3. Data model (core tables; every table has `tenant_id`, `created_at`, and is append-only where marked)

**Configuration (Doctrine Pack, versioned, audit-logged)**
- `tenants`, `doctrine_packs(version, json)`, `thresholds(key, value, version)`
- `entities(id, type, name, aliases[], languages[])`; `entity_relations(entity_id, tag: ally|supporting|opposition|neutral|…, valid_from, valid_to)`  (history preserved: story 14)
- `watch_terms(entity_id, term, script, kind)`; `lexicon(term, language, meaning, sarcasm_hint, examples[])` (Tone Lab)
- `tiers(account_or_outlet_id, tier, reason, set_by, valid_from)`; `tier_audit_log`
- `reaction_rosters(story_type, expected_reactor_ids[])`; `tvk_leader_accounts` (generic: `principal_accounts`)
- `recipients(person, channel, routing_rules, quiet_hours, role)`; `calendar_events(name, start, end, baseline_mode)`

**Content**
- `raw_items(id, source, payload, fetched_at, hash)`  [immutable]
- `accounts(id, platform, handle, followers, created_at, notable_flag, authenticity_score, tier_id, public_actor bool)`
- `outlets(id, name, type, language, partisan_flag, reach, feed_status, crawl_health)`
- `mentions(id, raw_item_id, source_type, account_id|outlet_id, text_snippet, url, lang, posted_at, stage_timestamps jsonb, engagement_series)`
- `labels(id, mention_id, entity_id, polarity, sarcasm, attack, claim_backing, reason, confidence, model_ver, prompt_ver, config_ver, status)`  [append-only]
- `corrections(id, label_id, new_values, by, at, reason, second_reviewer, status)`  [append-only]
- `stories(id, title, started_at, origin_mention_id, status)`, `story_mentions`, `story_snapshots(time series: reach by source type, velocity)`

**Detection and action**
- `alerts(id, type, severity, tier, story_id, state, explanation, evidence_ref, created_at)`; `alert_events(ack, assign, clear, escalate)`
- `reactors(story_id, actor_id, stance, tier, first_seen)`; `principal_responses(story_id, actor_id, mention_id|manual, at)`
- `response_opportunities(id, story_id, kind, trigger_time, clock_state, status, outcome, time_to_response)` (Mileage Ledger)
- `campaigns(id, cluster_id, score, confidence, organic_index, state)`, `campaign_accounts`, `campaign_signals`, `attributions(campaign_id, party, evidence, confirmed_by, reason, at)`
- `delivery_log(alert_id, recipient, channel, status, attempts, receipt)`

**Learning and trust**
- `gold_set(item_ref, labels_by_annotator[], consensus, language)`, `eval_runs(capability, language, f1, precision, recall, version)`
- `capability_licences(capability, language, stage, bar, since, reason)`; `licence_history`
- `agent_conversations`, `agent_memory(fact, provenance, edited_by, version)`, `agent_eval_cases`

**Operations**
- `source_health(source, status, last_ok_at, lag, note)`; `usage_meter(source, period, units, cap)`; `audit_log(actor, action, object, before, after, at)`
- `drill_scenarios`, `replay_runs`, `filter_audit_samples(sampled_item, reviewer_label, missed bool)`

---

## 4. Security and privacy
- Roles: Viewer, Analyst, Lead, Admin, CM-view, Minsky-operator. SSO/MFA; session limits for wall tokens.
- Presentation-safe filter at API layer; private-citizen handles scrubbed; internal notes hidden.
- Staff-only; no links to citizen/petition records; no person-centred views of private individuals.
- Encryption in transit and at rest; per-tenant keys; secrets in a vault; audit-log immutability.
- DPDP: purpose limitation, retention schedule, deletion on request path, breach runbook.

## 5. Agent safety design
- Ingested content wrapped in delimiters and passed as data; instructions are never taken from it.
- Tools: read-only query tools (count, compare, search, fetch Story/evidence) and `send_internal_alert` limited to the recipient allowlist. No outbound social, email, or arbitrary HTTP tool.
- Numbers only from tool results; answers must carry citation ids; a validator rejects uncited claims.
- Injection test-suite (posts with "ignore your instructions", hidden text in OCR, poisoned articles) in CI.

## 6. Failure behaviour (each has a visible state)
| Dependency | Failure | Visible behaviour |
|---|---|---|
| X API | outage / rate limit / cap | "X data delayed since HH:MM"; sampling labelled; admins alerted at 50/80/95% |
| News source / crawler | feed stale / site changed | per-source health; never "no new articles" |
| E-paper / OCR | missing edition / low confidence | "check scan"; edition status |
| AI model | outage / slow | dashboards continue; co-worker says its data is stale; rules-only alerts keep running |
| WhatsApp | send failure | retry, in-app "WhatsApp not delivered", lead notified |
| Queue/DB | lag | Pulse Strip shows pipeline lag per stage |

## 7. Observability
Per-stage latency histograms, p95 post-to-screen, per-source freshness, cost per item/stage, alert precision (acted vs dismissed), WhatsApp read rates, correction rates per language/entity/person, licence report cards.
