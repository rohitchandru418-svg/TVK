# NAADI: Narrative Operations Room
*(working title; "Naadi" = pulse. Rename freely. Per-client name and avatar are settings, not code.)*

Concept document for the TVK War Room Social Listening PRD (Phase 1, X + News). Written 2026-11-04.

---

## 1. The reframe: this is not a listening tool

Every off-the-shelf tool answers one question: **"what are people saying?"**
The PRD's real problem is different. Look at the real incident (the councillor remark):

- Nobody missed *negative volume*. They missed that **everyone else was condemning it while TVK stayed silent**.
- The loss was **timing**: the political mileage had a window, and the window closed.

So the product's centre of gravity is not sentiment. It is three questions, in this order:

1. **What is happening?** (Stories, fused across X, news, TV, print)
2. **Does it matter, and is it real?** (influence-weighted, organic vs coordinated)
3. **Do we need to move, and how long do we have?** (response window, who has spoken, who has not)

Question 3 is what no standard platform does. It is the spine of this product.

> **Product thesis: Naadi turns public conversation into a clock, a roster and a receipt.**
> A *clock* (how long the window stays open), a *roster* (who has reacted and who has stayed silent), and a *receipt* (the evidence behind every number).

---

## 2. Signature ideas (what makes it unique)

Each idea maps to PRD stories so nothing here is scope creep. These are *how* we deliver the stories in a way no standard tool does.

### 2.1 Mileage Clock (stories 31, 32, 5)
Every Story carries a live **response window**: *Open → Closing → Closed*, with a predicted peak shown as a band, not a false-precision point. Staff stop asking "is this big?" and ask "how long do we have?". The wall sorts by *window closing soonest*, not only by size.
- Built from the Story's velocity curve compared with similar past Stories (type, source tier).
- Cold start: transparent heuristics with a wide band, labelled "early estimate".

### 2.2 Reaction Roster: silence as a signal (stories 13, 31)
For each Story, a roster of **expected reactors** (allies, opposition, national media, state media, public figures, TVK leaders), each shown as *spoken / silent / not yet*. "Others are condemning, TVK silent" becomes a first-class, visible state instead of something a human must notice.
- Expected reactors come from the client's Doctrine Pack (see 2.9).
- TVK's own status is read from TVK leaders' accounts: "No TVK response yet" flips to "Responded by <name> at HH:MM".

### 2.3 Origin Trace ("Patient Zero") (stories 15, 19, 33)
For any Story: **where did it start, and how did it travel?** A propagation tree across event video, TV YouTube clip, X, news, e-paper. It answers "who broke it?" and "did print get there before X?", which also feeds the "Caught first" metric.

### 2.4 Constellation: campaign view (stories 10, 11, 12)
Coordinated campaigns shown as a **network constellation**: nodes sized by influence, ringed by account age, edges for shared text / mutual boosting, with a timing strip underneath.
- **Organic-ness index:** compare the cluster against what an *organic* hashtag looks like (age spread, text diversity, reply ratio). The question shifts from "is it suspicious?" to "how far from organic?".
- **Attribution gate:** UI says "possible coordination" until a named lead confirms. The system cannot name a party; the data model forbids it without a confirmation record.

### 2.5 Coalition Seismograph (story 13)
Not a generic negativity count. Per ally, per issue, it detects **tremors**: an ally's public figures shifting tone against *their own baseline*, even when still mild. Coalition risk is about change, and the first tremor is the alert.

### 2.6 Tone Lab: Tamil-native, team-taught (stories 24, 25, 26)
A workspace where the team curates **Tanglish idioms, sarcasm patterns and nicknames**, with examples. The system shows "how I read this" with the reason. Corrections land here and feed the gold set. Accuracy is tracked per language, so Tanglish is a first-class citizen, not an afterthought.
- Label schema fix: treat **polarity** (pos/neg/neutral/mixed) and **sarcasm** as separate fields. The PRD lists "sarcastic" as a stance; splitting them removes ambiguity ("sarcastic support" is positive polarity + sarcasm=true).

### 2.7 Licence to Operate: the trust ladder as a product (stories 34, 35)
Each capability (Tamil labels, spike alerts, campaign flags, response suggestions, briefs) has a **licence**: *Probation (Assist) → Licensed (Trusted) → Autonomous (Co-worker)*. Admin sees a **report card** per capability: error trend, sample size, bar, days at current level. Licence suspension is automatic and visible ("Campaign flags back on Probation: precision fell to 0.61 vs bar 0.70").
This turns the PRD's "earn trust" principle into something the war room can *see* and the founder can show to leadership.

### 2.8 Time Machine: replay, calibration, drills (must-pass tests, calibration)
A first-class **replay engine** that re-runs history from 2026-05-10 (or any window) through the live pipeline with chosen thresholds.
- **Calibration:** "what would this threshold have alerted on, and what would the team have done?"
- **Must-pass tests:** the councillor incident and the one-post-that-mattered are *replay fixtures* that run on every release.
- **Drill mode:** inject a clearly labelled synthetic event to measure the team's reaction time. Drill alerts are tagged DRILL and go only to a drill recipient list, never to real recipients by accident.

### 2.9 Doctrine Pack: how it generalises (Built-to-generalise)
Everything client-specific lives in one versioned, importable **Doctrine Pack**:
- entities and relationship tags (ally/opposition → competitor/regulator for a business)
- standing tiers, expected-reactor rosters, watch terms, Tanglish/lexicon seeds
- alert recipients and routing, thresholds, vocabulary ("CM summary" → "leadership summary")
- co-worker name, avatar, voice, theme tokens

A new client = a new Doctrine Pack plus a gold-set sprint. No code changes. This is both the engineering rule and the commercial story.

### 2.10 Pulse Strip: the quiet-screen guarantee (story 28)
A thin heartbeat line across the wall: one trace per source (X, each news tier, e-paper, YouTube, WhatsApp delivery, AI model). A flat or stale line is *visible from across the room*. "A quiet screen never fools us" becomes a design language, and it explains the product name.

### 2.11 Receipts: every number is a doorway (story 3, 7)
Every figure opens a drawer: the Mentions behind it, weighted next to raw, top three contributors, reasons on labels, original link, and what changed since last view. No number without a receipt.

### 2.12 Mileage Ledger: learning what deserves a response (stories 32, metrics)
Every response opportunity ends with an outcome: *acted (with time-to-response)*, *not needed*, *missed*. Monthly, the ledger shows **opportunities caught first vs lost**, and trains the queue on what the war room considers response-worthy. It is also the honest answer to the founder's "how much mileage did we capture?".

### 2.13 Dawn Brief and Dusk Baton (stories 2, 22)
- **Dawn Brief (06:30):** what happened overnight, any campaign, what's open, what to watch today.
- **Dusk Baton:** shift handover with owners, open alerts and watch items.
- **CM Card:** one screen, three changes at most, one tap to evidence, Tamil or English.

---

## 3. Product surfaces

| Surface | Who | Feel |
|---|---|---|
| **Wall** (big screen) | Whole war room, visitors | Calm, glanceable from 6 m, max 3 alerts, Pulse Strip, presentation-safe switch |
| **Desk** (web app) | Media, social, content teams, leads | Dense, fast, bilingual, every number a doorway; Story board, Constellation, Roster, Tone Lab, Time Machine |
| **Pocket** (WhatsApp + phone web) | Team leads, Sajan, AadhavArjuna | Short, actionable, evidence one tap away |
| **CM Card** | CM Vijay | 30-second read, three changes, a decision list |
| **Co-worker** (elephant avatar) | Everyone | Present in every surface: explains, answers in Tamil or English, drafts briefs. Never publishes. |

---

## 4. How Naadi differs from a standard platform

| Standard listening tool | Naadi |
|---|---|
| Counts mentions and sentiment | Weighs *who* said it, and shows *how long we have* to respond |
| Alerts on volume spikes | Alerts on **silence while others react**, single high-influence posts, tremors, coordination |
| Treats all accounts equally | Influence, tiers and outlet weights are visible, editable, versioned |
| English-first | Tamil, English and Tanglish measured separately, taught by the team |
| Black-box sentiment | Every label has a reason; every correction is kept, audited and bias-guarded |
| Dashboard | Operations room: acknowledge, assign, hand over, learn |
| Static accuracy claims | A licence ladder with live report cards per capability |
| Can't prove thresholds | Time Machine replays history to prove them |
| One client | Doctrine Pack: new client = configuration |

---

## 5. What we deliberately do not do (Phase 1)
- No posting, replying or publishing. The co-worker has no such tool at all (a capability absent, not merely a rule).
- No private-citizen profiles; no views centred on individuals who are not public actors.
- No automated attribution of a campaign to a party.
- No multiple X apps to get past limits.
- No hard-coded TVK anything.

---

## 6. Parked ideas for later phases (do not build now)
- **Counter-campaign sandbox:** drafts and simulations, with human approval, within platform rules.
- **Pre-bunk watch:** claims likely to be pushed at upcoming events, based on the calendar and history.
- **Constituency lens:** district-level views when reliable geo signals exist (X geo is weak; do not promise it).
- **WhatsApp, Instagram and Facebook sources** (own legal review).
- **Cross-client benchmarks** (only with explicit consent and anonymisation; never mix corrections or data).
- **Voice interface** for Tamil queries on the wall or in the car.

---

## 7. The one-paragraph pitch
*Naadi is the war room's pulse. It watches X, news, TV, e-paper and clippings in Tamil, English and Tanglish; weighs who is speaking; fuses it into Stories; tells you how long the window to respond stays open and who has already spoken; spots coordinated pushes; and shows the evidence behind every number. Its elephant co-worker explains, drafts and learns from every correction, and earns more responsibility only as its measured accuracy on your own corrections proves it. It never posts. It makes sure TVK is never the last to know.*
