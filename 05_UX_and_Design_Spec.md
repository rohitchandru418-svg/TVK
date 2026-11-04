# UX and Design Spec: Naadi

Design principle: **calm on the wall, dense on the desk, brief in the pocket.** Easy to understand on top; statistically correct underneath.

---

## 1. Design language
- **Name motif:** *Naadi* = pulse. Pulse Strip, a thin heartbeat line, is the signature element across surfaces.
- **Tamil-first typography:** Tamil-capable families (Noto Sans Tamil / Anek Tamil / Mukta Malar class), tested for ascenders in dense tables; English paired font; numerals shown per user preference (Latin or Tamil digits).
- **Theme tokens per client:** colours, logo, avatar come from the Doctrine Pack. TVK default theme derived from official artwork supplied by the party. The elephant avatar is the supplied artwork, never redrawn.
- **Wall:** dark, high contrast, no scrolling, motion only for new T1 alerts (a slow pulse, not a strobe: avoid seizure risk and fatigue).
- **Desk:** light or dark theme, keyboard-first for leads, dense tables, split panes.
- **Colour use:** red/amber reserved for severity; never use colour alone for state (add icons and words). Sentiment colours colour-blind safe.
- **Vocabulary from settings:** "party", "ally", "CM summary" are label keys, not strings.

## 2. The Wall (big screen), 16:9, readable from ~6 m

```
┌───────────────────────────────────────────────────────────────────────────────┐
│ Pulse Strip:  X ▂▃▂▃  News ▂▃▂▂  TV ▂▂▃  E-paper ▃  WhatsApp ✓  AI ▂▃   (stale = flat red) │
├───────────────┬───────────────┬───────────────┬───────────────────────────────┤
│ TVK           │ CM Vijay      │ AadhavArjuna  │  ALERTS (max 3, ranked)       │
│ +34% / −41%   │ …             │ …             │  ① T1 Response opportunity    │
│ of 812 men.   │               │               │     "no TVK response yet"     │
│ upd 10:07     │               │               │     Clock: OPEN ▮▮▮▯          │
│ weighted | raw│               │               │  ② Influence: journalist …    │
├───────────────┴───────────────┴───────────────┤  ③ Possible coordination …    │
│ ALLY MOOD  (seismograph per ally, tremor ◉)   ├───────────────────────────────┤
│                                               │  RISING STORIES (by window    │
│                                               │  closing soonest)             │
└───────────────────────────────────────────────┴───────────────────────────────┘
```
Rules: every figure shows "of N mentions, last updated HH:MM"; below 30 shows "not enough data"; weighted and raw toggled together; stale panels turn grey with "data delayed since HH:MM (source)"; **presentation-safe toggle** (one tap, lead role) limits to aggregates, public actors and Story titles.

## 3. The Desk (web app)

Navigation (Tamil/English): **Now · Stories · Alerts · Response Queue · Allies · Campaigns · Tone Lab · Clippings · Ask · Settings · Time Machine · Admin**

- **Now:** the same picture as the Wall, plus my assignments and open alerts.
- **Stories board:** columns *Open window / Closing / Closed / Unattributed*; each card shows the **Mileage Clock**, reach split (outlets | posts | print), severity chip, and TVK status.
- **Story page (the heart):**
  - Header: title, started-at, clock state, severity, owner.
  - **Reaction Roster** (spoken ✓ / silent ○ / not yet ·) with TVK status line.
  - **Origin Trace** tree (first seen → spread).
  - **Receipts:** list of Mentions with author/outlet, engagement, label, reason, original link, corrected-by marks.
  - **Co-worker panel:** two-line explanation, recommendation (never an action), "ask a follow-up".
  - Actions: acknowledge, assign, merge/split, mark acted / not needed.
- **Allies:** Ally Mood seismograph per ally, per issue; people and posts; tremor history.
- **Campaigns / Constellation:** network view (nodes sized by influence, ringed by account age; edges = shared text or boosting); timing strip; **organic-ness index**; evidence pack; attribution panel (locked until lead confirms; shows who and why).
- **Tone Lab:** lexicon list, example posts "how I read this", sarcasm cases, per-language accuracy, queue of low-confidence items.
- **Clippings:** drag-and-drop batch upload, OCR status, "check scan" queue, page preview.
- **Ask:** chat with the co-worker, language auto-follows the question, citations as chips.
- **Time Machine:** pick a window and thresholds, run replay, see "would have alerted" vs actual, DRILL launcher.
- **Settings:** all Doctrine Pack data with version history, diff view, audit.
- **Admin:** source health, crawler health, X usage vs cap, missed-item audit, licence report cards, correction/bias dashboard.

## 4. CM Card (read in 30 seconds)
- Header: date, "what changed since <time>".
- At most **3 changes**, each: one-line headline, one-line cause, a confidence chip, one tap to evidence.
- "Needs a decision" list (if any).
- Ally-blaming items show "reviewed by <name>" or are held.
- English/Tamil switch; large type; no raw handles of private individuals; presentation-safe by default.

## 5. Pocket: WhatsApp templates (Tamil and English; wording to be confirmed with the war room)

**Response opportunity (to Sajan)**
```
🐘 Response opportunity · OPEN window
<Kind: condemn> · <Entity>
What: <one-line summary> · <link to clip/article>
Already reacting: <3 names/outlets>
TVK response: none yet · started <N> min ago
Tap for evidence: <short link>
```
**Influence trigger (to leads)**
```
🐘 Alert · High influence
<Handle/outlet> (<tier>, <followers>) posted against <Entity>
<N> reposts in <M> min · reason: verified, national media
Evidence: <link>
```
**Dawn Brief:** 3-5 lines, one link. **Delivery rules:** individuals not groups; quiet hours per recipient; escalate to the next lead if unacknowledged after the set time; failed delivery shows in-app.

## 6. States and microcopy
- **Empty:** "No Mentions in the last 15 minutes. Source health: X ✓ News ✓" (never just blank).
- **Stale:** "Data delayed since 10:07 · X".
- **Low sample:** "Not enough data (18 of 30 needed)".
- **Corrected:** "Corrected by Meena · original: Negative · reason: sarcastic support".
- **Possible coordination:** "Possible coordination · confidence Medium · not attributed".
- **Co-worker uncertainty:** "I can't say from the data we have. I found 12 posts; here's what they show."

## 7. Accessibility and bilingual rules
Full keyboard use; screen-reader labels; contrast AA on desk, AAA targets on wall; Tamil line-height and word-wrap tested; mixed Tamil/English strings render correctly; date/time in IST with a 24 h option.

## 8. Co-worker presence
The elephant avatar appears as a small presence on every surface; never hides evidence behind chat; tone: brief, respectful, plain Tamil/English; "name and voice to be confirmed with the client" (setting, not code).
