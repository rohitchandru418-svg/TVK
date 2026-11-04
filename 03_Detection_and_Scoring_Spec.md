# Detection and Scoring Spec: Naadi Phase 1

All numbers are **defaults for calibration**, not decisions (PRD convention). Every constant is a versioned setting in the Doctrine Pack and editable without a deploy. Every output stores the config version used, so any score can be reproduced.

---

## 1. Vocabulary
- **Mention:** one item matched to a tracked entity.
- **Story:** fused group of Mentions about one happening.
- **Reactor:** a public actor or outlet that has reacted to a Story (allies, opposition, media, public figures, TVK leaders).
- **Ia:** account/outlet influence (0-1). **N:** negativity (0-1). **M:** momentum/velocity (0-1). **E:** entity weight.

---

## 2. Label schema (per Mention, per entity)
```
entity_id
polarity        : positive | negative | neutral | mixed
sarcasm         : bool            # separate from polarity
attack          : bool            # directed attack on the entity
claim_backing   : on_record | named_source | anonymous_insinuation | satire | none
language        : ta | en | tanglish | mixed
reason          : short text, shown to users
confidence      : 0-1
model_version, prompt_version, config_version
```
Hard rule: sarcasm *flips* effective polarity only through the label itself (the model must output the effective polarity); the flag exists for audit, analytics and Tone Lab.

---

## 3. Influence score Ia (account or outlet)

```
Ia = clamp( 0.30*F + 0.20*V + 0.35*T + 0.15*C ) * A
F = min(1, log10(1 + followers) / 7)             # 10M followers -> 1.0
V = staff "notable/official" flag (1.0) | paid-verified only (0.3) | none (0)
T = tier weight:
      national media 1.00 | party leader/official handle 0.90 | state media 0.80
      influencer 0.60 | local/other known 0.40 | unknown 0.10
C = historic reach consistency (median engagement relative to followers, squashed 0-1)
A = authenticity factor 0.30-1.00 (down-weights suspected bots; shown in UI)
```
Outlets use outlet-level T and reach; party-affiliated outlets carry a "partisan" flag, shown with the reason, and a configurable weight modifier (default 0.7 for neutrality-sensitive aggregates).

Weights are versioned; every change writes to the tier/weight audit log with name and time.

---

## 4. Negativity N and Momentum M

```
Npol  = negative 1.0 | mixed 0.4 | neutral 0 | positive 0     (effective polarity, sarcasm already applied)
Natk  = 0.6 + 0.4*attack                                       (attack flag lifts)
Nclm  = on_record 1.0 | named_source 0.9 | anonymous_insinuation 0.8 | none 0.8 | satire 0.4
N     = Npol * Natk * Nclm

eng_w = (reposts*1.0 + quotes*1.5 + replies*0.7 + likes*0.1) per minute since post, weighted by engager influence where available
M     = 1 - exp( -eng_w / k_account )          # k_account scales to the account's usual engagement, so
                                                # a 40-follower account needs far more to count
M_floor = 0.5 if Ia >= 0.80 else 0             # the "one post that mattered" path
```

---

## 4b. Severity

```
S_item  = Ia * N * max(M, M_floor) * E
E       = principal entity (TVK, CM, minister) 1.0 | ally 0.9 | opposition 0.7 | neutral 0.4
S_story = 1 - PRODUCT over top-k items (1 - S_item)          # "noisy-or": many mid-severity items add up, capped at 1
```
Tiers (default): **T1 >= 0.40** (wall flash + WhatsApp to leads), **T2 0.20-0.40** (in-app), **T3 < 0.20** (feed only).
Sanity examples: verified national journalist (Ia 0.90, N 0.90, M 0.60, E 1.0) = 0.49 -> T1. Account with 40 followers (Ia 0.10, N 0.90, M 0.20) = 0.02 -> T3.

Wall rules: show at most 3 (default); group related; ack/assign clears for everyone.

---

## 5. Volume spike (story 9)

- Bin by 15 minutes. Baseline = same hour-of-day (and event-aware window if the calendar marks one) over the last 14 days.
- Use **robust statistics** (median + MAD) because counts are heavy-tailed; for small counts use a Poisson or negative-binomial interval.
- Fire when **all** hold: z >= 3, current >= 2x baseline, >= 25 negative Mentions in 15 minutes.
- Output: baseline, current, top posts driving it, weighted vs raw.
- Calendar events (rally, assembly session, results day) use their own baseline; the first occurrence uses a pooled baseline and is labelled "early baseline".
- "A change is called a change only when it clears a significance test or confidence interval": the same rule applies to the sentiment trend arrows on the wall.

---

## 6. Response Opportunity engine (stories 31-33; the councillor incident)

### 6.1 Candidate detection
A Story becomes a **candidate** when any holds:
- an item has `attack = true`, `N >= 0.6`, against a principal entity and comes from an event report, TV clip, or a speaker in the registry; or
- a positive trigger exists (award, achievement, welfare milestone, a death or tragedy needing condolence); the engine labels the suggested kind: *condemn, rebut, congratulate, condole, claim-credit*.

### 6.2 Reaction score
```
R = SUM over DISTINCT reactors (weight by reactor tier) in the last 60 minutes
    national media 1.0 | state media 0.8 | ally public figure 1.0 | other-party leader 0.8
    recognised public figure 0.6 | influencer 0.4
```
### 6.3 Trigger
Fire a **Response Opportunity** when:
```
candidate AND R >= R_min (default 2.0) AND tvk_response == none AND clock != Closed
```
Alert goes to **Sajan** (WhatsApp) within 5 minutes of the trigger, containing: the clip/article link, kind of suggested response, who is already reacting (names and tier), time since start, "no TVK response yet", and the Mileage Clock state.

### 6.4 TVK response detection
- Registry of **TVK leader accounts** (Doctrine Pack).
- A TVK leader post counts as a response when it (a) quotes/replies to a Story source, **or** (b) matches the Story semantically (embedding similarity >= tau, default 0.82) and mentions the target entity/topic, **after** the Story start.
- Manual **"responded elsewhere"** button (press meet, TV) since not all responses are posts. When detected: status becomes "responded by <name> at HH:MM" and alerting stops.

### 6.5 Mileage Clock
```
velocity(t) = weighted new reactions per 10 min
state: OPEN while velocity is rising or within 80% of peak
       CLOSING when velocity < 50% of peak
       CLOSED when velocity < 15% of peak or age > 24 h (default)
predicted peak: fitted from similar past Stories (type, source tier, hour); shown as a band; labelled "early estimate" when < N similar Stories exist
```
### 6.6 Response queue
Queue sorted by *window closing soonest*, then severity. Lead choice (*acted / not needed*) is stored with reason and feeds the next calibration (outcome label for "worth a response").

---

## 7. Ally tremor and Ally Mood (story 13)

- For each ally entity, track **public figures** (official handles, MPs, MLAs, spokespeople) and their stance toward TVK/CM/minister.
- Per figure: rolling 30-day baseline of stance toward our entities. **Tremor** when a figure's 6-hour tone shifts by z >= 2 against their own baseline, or a first-ever negative from a top-tier ally figure.
- **Ally threshold (default):** alert when >= 2 distinct ally figures show a tremor within 6 hours, or 1 top-tier ally leader does.
- Ally Mood panel shows: ally -> people -> posts -> issue, a before/after share of negative ally mentions, and "N = sample size".
- CM-summary rule: any ally-blaming text is held for lead review.

---

## 8. Campaign detection (stories 10-12)

Cluster candidates: accounts posting within a short window with the same hashtag or near-duplicate text.

Signals (each 0-1; shown to users as evidence):
| Signal | How |
|---|---|
| Account-age skew | share of accounts < 90 days old vs. organic baseline for that hashtag size |
| Text near-duplication | MinHash / embedding cosine >= 0.92 clusters; share of cluster in top 3 phrasings |
| Timing synchrony | share of posts within the same 60-second bins; burst vs. diurnal pattern |
| Mutual boosting | density of reciprocal repost/reply edges inside the cluster |
| Low-history / profile similarity | default avatars, handle patterns, empty history, same client app |
| Network smallness | hashtag volume explained by few accounts relative to total |

```
campaign_score = logistic( b0 + sum(w_i * signal_i) )     # weights fitted on labelled examples, versioned
confidence     = Low | Medium | High  (calibrated bands; High needs >= 3 independent signals)
min cluster size (default 30 accounts) to alert; below that, observe only
organic_index  = distance of the cluster's age spread / text diversity / reply ratio from organic hashtags of similar size
```
**Attribution gate:** campaigns display "possible coordination"; an `attribution` record (party, evidence, confirming lead, reason, time) must exist before any text names a party. Without it, UI and the agent cannot output attribution. Tested.

Privacy: evidence shows account handles for *public actors and clusters of accounts as accounts*, but no private-citizen profile pages, no cross-linking to petition or citizen records; presentation-safe mode hides handles.

Spread tracking: Mentions per hour, new accounts joining, our response Stories, on one timeline.

---

## 9. Replay fixtures for the two must-pass scenarios

### Scenario A: the councillor incident
```
T+00:00  Rival-party councillor speaks ill of CM at a rival function; deputy general secretary on stage.
T+00:07  Event video clip posted to X by a local account (low influence)             -> candidate created
T+00:12  YouTube news channel upload quotes the remark                                -> Mention (quoted line, speaker)
T+00:15  Two news websites publish; three state-level journalists condemn             -> R rises
T+00:22  An ally MLA and a national media handle condemn                              -> R >= 2.0
T+00:23  TRIGGER: no TVK leader post found
EXPECT   WhatsApp to Sajan by T+00:28 at the latest (<= 5 min from trigger), containing
         clip link, who is reacting, "no TVK response yet", clock = OPEN
T+00:40  TVK leader replies -> status "responded by <name> at 00:40"; no more alerts
ASSERT   (a) alert latency <= 5 min; (b) content fields present; (c) no repeat alert after response;
         (d) the Story shows on the wall as a T1/T2 per severity; (e) ledger records time-to-response.
```
### Scenario B: the one post that mattered
```
T+00:00  Verified national journalist (1.2M followers, national media tier) posts sharp criticism of CM
T+00:05  50 reposts; no volume spike
T+00:20  4,000 reposts
ASSERT   Wall flash and WhatsApp to leads at or before T+00:05 (influence floor path) with reason
         "verified, 1.2M followers, national media, N reposts in M min"; a 40-follower account with the
         same text does not alert; the flash clears for everyone on acknowledge ("taken by <name>").
```
Plus negative fixtures (must NOT alert): film-fan "Vijay" chatter; a sarcastic *supportive* Tanglish post; a legitimate organic hashtag with diverse accounts.

---

## 10. Calibration protocol

1. **Gold set:** minimum starting size ~600 English, ~900 Tamil, ~900 Tanglish items, stratified by entity, source and stance; 20% dual-labelled; inter-annotator agreement (Cohen's kappa) >= 0.70 before use.
2. Run all filters/labels on the gold set; set per-language bars from F1 results; set confidence thresholds for escalation.
3. **Replay** the first weeks of live data via Time Machine: tune thresholds against act/dismiss decisions; target alert-actioned share >= 60% (default).
4. After two weeks of live data, review thresholds together with the war room (PRD decision 3).
5. Ongoing: weekly accuracy chart, weekly filter-out audit, monthly response-opportunity review.

## 11. Licence ladder rules (defaults)
- **Promotion:** accuracy >= bar for >= 14 days with >= 200 reviewed items (per capability and language).
- **Demotion:** accuracy falls below bar - 0.03 for 3 consecutive days, or a high-severity error occurs; automatic; leads see the reason.
- Bars are set in calibration, never guessed.
