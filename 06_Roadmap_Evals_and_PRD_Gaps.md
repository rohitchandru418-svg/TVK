# Roadmap, Evals and PRD Gaps

## 1. Slices and exit criteria (relative effort; the CTO sets dates)

| Slice | Delivers | Exit criteria |
|---|---|---|
| S0 Foundations | Tenancy, Doctrine Pack loader, config-as-data, audit, auth, i18n shell, telemetry, Pulse skeleton | Two packs (TVK, fictional business) load without code changes; stage timestamps flow end to end on fakes |
| S1 X ingest + filter | Adapter + fakes, raw store, wide filter, usage meter, backfill tool | Backfill from 2026-05-10 dry-run costed; usage meter alerts at 50/80/95%; filter recall measured on a seed set |
| S2 Label + Tone Lab + corrections | Label schema, cheap/strong routing, reasons, correction UI, bias guard, gold-set tool | Inter-annotator kappa ≥ 0.70; per-language F1 reported; corrections append-only |
| S3 Weigh + Wall v1 + Receipts | Influence, weighted-beside-raw, denominators, presentation-safe | Every number opens receipts; "not enough data" below 30; API-level presentation-safe tests pass |
| S4 Alerts (must-pass B) | Severity, influence floor, wall flash, WhatsApp, ack/assign, failure path | Scenario B replays green at ≤5 min; negative fixtures stay quiet |
| S5 News + Stories | Feeds, crawlers, YouTube, clustering, merge/split, Origin Trace | Crawl error never reads as "no new articles"; "9 outlets, 1,200 posts" fixture passes |
| S6 Co-worker v1 | Grounded Q&A, citations, Tamil/English, evals, degraded mode | Eval suite thresholds met; injection suite green; first answer ≤10 s |
| S7 Spike + Ally | Baselines, calendar, tremor, Ally Mood | Spike precision measured on replay; ally threshold reviewable |
| S8 Response Opportunity (must-pass A) | Roster, Mileage Clock, queue, TVK-response detection, Time Machine | Scenario A replays green; no repeat alert after TVK response; ledger records outcomes |
| S9 Constellation | Campaign signals, evidence pack, organic index, attribution gate | Attribution cannot be output without a confirmation record (tested); precision on labelled campaigns reported |
| S10 E-paper + clippings + OCR | Ingest, Tamil OCR bake-off, "check scan" | Tamil numerals read correctly on test pages; per-publisher terms flag enforced |
| S11 Trust + briefs | Licence ladder, Mileage Ledger, Dawn Brief, Dusk Baton, CM Card | Promotion/demotion works on replay; CM Card ≤3 changes; ally-blame held |
| S12 Hardening + launch | Load and failure drills, security, DPDP, display-rule check, calibration | Section 12 of the master prompt satisfied |

Parallel tracks from day 1: **X contract talks**, **gold-set labelling sprint**, **outlet register and feed status**, **WhatsApp template approval**, **OCR bake-off**, **retention/DPDP review**.

## 2. Evaluation plan
- **Label accuracy:** F1 per language/entity on the gold set; confusion on sarcasm; weekly chart (model-only vs corrected).
- **Filter recall:** weekly stratified audit of filtered-out items; target trending down.
- **Alerts:** precision (acted vs dismissed), time-to-alert on real events, undelivered rate.
- **Campaigns:** precision = share of flagged campaigns a lead confirms; false-attribution incidents must be zero.
- **Co-worker:** fixed Q&A set (Tamil + English), accuracy, citation validity, correct "I don't know", prompt-injection suite; run on every model/prompt change.
- **Freshness:** p95 post-to-screen from stage timestamps; spike-day load test.
- **Business:** daily use, response opportunities caught first, time from opportunity to TVK response, analyst hours saved.
- **Stop/rollback triggers (PRD):** most big-screen alerts dismissed after calibration; Tamil/Tanglish accuracy below bar; privacy incident.

## 3. Gaps and ambiguities spotted in the PRD (with suggested resolutions)

1. **Response-opportunity trigger is undefined.** Proposed: reaction score R ≥ 2.0 from distinct reactors + no TVK response + window not closed (spec §6). Needs war-room calibration.
2. **Severity normalisation is undefined.** Proposed explicit formula and tier cut-offs (spec §4); the product must show components.
3. **"Verified" is weak on X** (paid verification). Use a staff "notable/official" flag; show reason text accordingly.
4. **"Sarcastic" is listed as a stance.** Split polarity and sarcasm; otherwise "sarcastic support" is ambiguous in aggregates.
5. **Detecting "TVK has responded"** needs the registry of TVK leader accounts and a rule linking a post to a Story; responses outside X (press meet, TV) need a manual button.
6. **WhatsApp constraints:** official API needs approved templates, opt-in, and generally individual recipients; the PRD's "team leads on WhatsApp" must be designed within this; costs and quality rating should be planned.
7. **Retention vs "keep everything":** raw storage conflicts with minimisation under DPDP unless a retention schedule is set; decide before ingestion.
8. **X display rules** for showing post text on a wall; confirm before launch.
9. **Backfill cost and feasibility** from 2026-05-10 under any X route; define scope (principal entities first, other parties later).
10. **Gold-set bootstrap:** nothing exists yet; a labelling sprint (3+ annotators, Tamil and Tanglish fluent) is on the critical path.
11. **News freshness floor** per source type is "owed" by the CTO; measure per outlet in the first two weeks.
12. **Ownership of attribution:** PRD recommends two named leads (one per shift); needs naming and a coverage plan.
13. **Political-sensitivity handling:** campaign flags and ally-blame can cause real harm if wrong; the confirmation gates, confidence bands and audit log are product requirements, not optional.
14. **Election-period obligations:** confirm any model-code-of-conduct or platform-policy constraints on how outputs may later be used when the counter-campaign phase arrives (out of scope now; keep the door visible).
15. **Alert recipients and routing by entity** (who gets AadhavArjuna-related vs CM-related vs ally) are not yet provided.

## 4. Open questions for the founder / war room (priority order)
1. Start X enterprise/vendor talks now? (recommended: yes)
2. Who are the two attribution-confirming leads (one per shift)?
3. Sajan's WhatsApp number, other leads, routing rules, quiet hours.
4. The outlet register, feed status, and e-paper subscriptions with terms.
5. The list of all parties and key leaders with relationship tags; TVK leader accounts for response detection.
6. Standing tiers (journalists, politicians, influencers).
7. Real past misses and a labelled starter set (clippings and posts the war room acted on).
8. Official elephant artwork; co-worker name and voice (candidate Tamil names to test: *Thunai* துணை "companion", *Yaanai* யானை "elephant"; client's call).
9. Acceptance of default thresholds as starting values (review after two weeks live).
10. Retention period preferences and who is the data owner for DPDP.

## 5. Risks and mitigations
| Risk | Mitigation |
|---|---|
| Confident wrong answers in Tamil/Tanglish | Gold set before launch; per-language bars; reasons on labels; second review for ally blame to the CM |
| Alert fatigue in campaign season | Severity tiers, wall cap, event-aware baselines, weekly tuning, must-pass alerts always fire |
| Coverage gap on the key day | Enterprise X, crawl health, print-first via e-paper/clippings, Pulse Strip |
| False campaign accusation | Attribution gate, confidence bands, evidence pack, zero-tolerance metric |
| Privacy incident | API-level presentation-safe, no private-citizen views, retention rules, audit |
| Scope creep into posting | No posting tools exist in Phase 1; reviewed at every release |

## 6. Week-0 checklist
- [ ] ADR drafts 001-014 circulated
- [ ] Doctrine Pack schema + two packs
- [ ] Replay fixtures A and B (spec §9)
- [ ] X vendor/enterprise outreach started
- [ ] Annotator hiring and gold-set guidelines (kappa target 0.70)
- [ ] WhatsApp Business onboarding + template drafts (Tamil, English)
- [ ] Outlet register confirmed; feed/crawl status sheet
- [ ] Retention/DPDP position agreed
