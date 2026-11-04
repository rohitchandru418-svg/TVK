[🐘 Naadi](#) [Design](#philosophy) [The Wall](#wall) [Pulse Strip](#pulse) [Entity Panels](#entity) [Alerts](#alerts) [The Desk](#desk) [Roster + Clock](#roster) [Campaigns](#constellation) [Tone Lab](#tone-lab) [Pocket](#pocket) [Time Machine](#time-machine) [Licence Ladder](#licence) [AI Patterns](#ai-patterns)

UI & Component Deep Spec · Phase 1

# Naadi War Room Design

AI-native operations room for TVK. Every component, every page, every AI interaction — deeply specified. Calm on the wall. Dense on the desk. Brief in the pocket.

**Version** 1.0 · Draft for CTO **Date** 2026-11-04 **Surfaces** Wall · Desk · Pocket · CM Card **Languages** Tamil · English · Tanglish

01 — Design Philosophy

AI-native means the AI fills every surface, not a tab

The co-worker (elephant avatar) is not a chatbot in a drawer. It is the layer that explains every number, reasons through every alert, and streams its thinking into the surface the person is already on. No surface is static.

The Wall

### Calm, glanceable

Readable from 6 metres. Max 3 alerts. No scrolling. Motion only for T1. The room must never feel like noise.

The Desk

### Dense, keyboard-first

Every number is a doorway. Split panes, bilingual, every label correctable, every alert assignable. 10-hour analyst home.

The Pocket

### Brief, one tap to evidence

WhatsApp templates. The lead must act in 90 seconds. Contains: what, who, clock state, evidence link. Nothing else.

Colour Tokens

Doctrine Pack overrides all

| Token | Default Value | Permitted Use |
| --- | --- | --- |
| --severity-t1 | #FF3B30 | T1 alert wall flash and border only |
| --severity-t2 | #FF9500 | T2 alert chip, closing window indicator |
| --severity-t3 | #34C759 | T3 feed chip, positive signal |
| --neutral | #8E8E93 | Stale data, low confidence |
| --surface-wall | #0A0A0F | Wall background (never lighter) |
| --accent | Doctrine Pack | TVK: #E8291E red + #1A1A2E deep navy |
| --pulse | #00D4FF | Pulse Strip trace across all surfaces |

Colour is never the only signal. Every severity state also uses an icon and a word. All sentiment colours pass WCAG AA.

Motion Rules

Zero decorative transitions

| Event | Animation | Constraint |
| --- | --- | --- |
| T1 alert | 2s slow border pulse, ease-in-out | Max 3 cycles then holds solid red border. Never strobe. |
| Pulse Strip | Smooth SVG path at 1 fps | Heartbeat, not noise |
| Metric update | Counter roll 400ms | Numbers roll, never snap |
| Desk panel slide | 150ms ease-out | No bounce |
| Drawer open | 200ms ease-out | No spring physics |

No motion on the wall except T1 pulse and Pulse Strip. prefers-reduced-motion: all animations skip to end-state.

Typography Scale

Tamil-first; English paired

| Use | Font | Size / Weight | Notes |
| --- | --- | --- | --- |
| Wall entity name | Noto Sans Tamil | 48px / 700 | Readable at 6 m on 55″ 4K |
| Wall metric | Inter | 72px / 800 | +/−41% |
| Wall label | Noto Sans Tamil | 20px / 400 | "of 812 mentions" |
| Desk body | Noto Sans Tamil / Inter | 14px / 400 | Ascenders tested in dense tables |
| Desk heading | Inter | 18px / 600 | Section titles |
| Desk stat | Inter | 24px / 700 | Key counts on Now page |
| Mono (timestamps, handles) | JetBrains Mono | 13px / 400 | All times in IST 24h |
| Chip / badge | Inter | 10px / 600 uppercase | T1 / SAMPLED / STALE |

Numeral preference (Latin or Tamil digits) is per-user setting via config.numerals. Numbers use Indian system (lakhs, crores) with Western notation in a tooltip.

02 — The Wall

16:9 big screen. Readable from 6 metres.

The wall is not a dashboard — it is a room-state. It answers one question: "where are we right now?" Every element is static until something genuinely warrants attention. Presentation-safe toggle hides private handles and internal notes in one tap.

🐘 Naadi War Room · IST 11:43 · ● Live

● X Live

News 10:51

X

News

TV / YT ·stale 10:51

E-paper ✓

WhatsApp ✓

AI ▌

TVK

−41%

+34% pos · of 812 men.

**Top:** @natnl_jrnlst

↑ +12% / 30m

upd 11:41 · X + News · 812 men.

CM Vijay

+18%

−22% neg · of 234 men.

stable

upd 11:40 · 234 men.

AadhavArjuna

not enough data

18 of 30 needed

upd 11:39 · 18 men.

ALERTS

① T1 · Response Opportunity

No TVK response · others condemning councillor remark

3 reactors · 00:23 ago

OPEN · closing \~18 min

② T2 · High Influence

Nat'l journalist (1.2M) posted against CM

verified · 4,000 reposts / 20 min

③ T3 · Coordination Signal

Possible coordinated push · confidence Medium

\~300 accts, 70% \<3 months old

Ally Mood

DMK

stable

Congress TN

◉ tremor

VCK

stable

Rising Stories (window closing soonest)

Councillor remark at DMK function · 9 outlets, 1,200 posts

OPEN · \~18m

Cabinet decision — Congress MLA posts · 2 outlets

CLOSING

Welfare milestone announced · 14 outlets

OPEN

Wall Layout Grid

16:9 · 3840×2160 target · no scrolling

┌─ Pulse Strip (40px fixed) ────────────────────────────────────────────┐ │ X ▂▃▂▃ News ▂▃▂▂ TV ▂▂▃ E-paper ✓ WhatsApp ✓ AI ▂▃ stale=flat│ ├──────────┬──────────┬──────────┬────────────────────────────────────────┤ │ ENTITY │ ENTITY │ ENTITY │ ALERTS RAIL (max 3, ranked T1→T3) │ │ TVK │ CM Vijay │ Aadhav │ ① T1 Response Opportunity │ │ −41% │ +18% │ no data │ "No TVK response · 3 reacting" │ │ of 812 │ of 234 │ 18/30 │ Clock: OPEN ▮▮▮▯ · \~18 min │ │ weighted │ │ │ ② T2 Influence · nat'l journalist │ │ raw │ │ │ ③ T3 Coordination signal · Medium │ ├──────────┴──────────┴──────────┼────────────────────────────────────────┤ │ ALLY MOOD (seismograph) │ RISING STORIES (by window closing) │ │ DMK ████████░░ stable │ ● Councillor remark · OPEN \~18m │ │ Congress ████░░░░ ◉ tremor │ ● Cabinet decision · CLOSING │ │ VCK ████████░░ stable │ ● Welfare milestone · OPEN │ └────────────────────────────────┴────────────────────────────────────────┘

| Zone | Height | Purpose | AI role |
| --- | --- | --- | --- |
| Pulse Strip | 40px fixed | Source heartbeat — a flat line means a problem | AI monitors pipeline lag per stage; stale state triggers when last-ok > threshold |
| Entity panels (×3) | \~55% of body | Weighted sentiment per principal entity | AI computes weighted score every 60s; confidence band determines if score shows or "not enough data" shows |
| Alert rail | \~55% of body | Top 3 alerts ranked by severity | AI ranks, groups related posts, writes 2-line explanation, flashes T1 border |
| Ally Mood | \~45% of body | Per-ally sentiment seismograph | AI detects tremors against 30-day ally baseline; tremor dot fires when z ≥ 2 |
| Rising Stories | \~45% of body | Stories by window closing soonest | AI predicts window close from velocity curve against similar past stories |

Default

Live data · all panels active

Stale Source

Panel grey · "data delayed since HH:MM · X"

Not Enough Data

Chip replaces % · never 0 or dash

T1 Flash

Border pulse 2s × 3 · then holds red

Presentation Safe

1-tap toggle · hides private handles + internal notes

Acknowledged

Flash clears · "taken by Meena 11:44"

03 — Pulse Strip Component

A flat line is never "quiet". It is a problem.

The Pulse Strip is a thin heartbeat line across the top of every surface. One trace per source. The wall must never fool anyone into thinking silence means nothing is happening.

Per-Source Trace Specification

component: PulseStrip

| Source | Live state | Stale state (after threshold) | Down state | Stale threshold |
| --- | --- | --- | --- | --- |
| X API | Animated bars, --pulse colour, height = recent volume relative to 15m baseline | Flat red bar · "X stale HH:MM" | "X outage" · red chip · admin alerted | 5 min since last item |
| News feeds | Animated bars, 70% opacity (secondary source) | Flat red · source name | Crawler health shown; "never 'no new articles'" | 60 min since last item (source-type floor from CTO) |
| TV / YouTube | Animated bars, 60% opacity | Amber flat bar · "TV stale HH:MM" | Per-channel health card in Admin | 120 min |
| E-paper | Static ✓ when today's edition ingested | "E-paper: no edition today" (expected delivery window per publisher) | OCR health flag in Admin | Expected delivery + 2h |
| WhatsApp delivery | ✓ · last delivered HH:MM | "WA undelivered" amber | "WA failed" red · in-app escalation | First retry fail |
| AI model | Animated bars --pulse colour; pulsing ▌ when answering | "AI stale" · co-worker shows "data may be old" | "AI down · rules-only alerts active" | 30s no response |

Implementation note: stage timestamps on every item make pipeline lag measurable. The Pulse Strip reads from source_health table, not from inference. Pulse Strip appears on Wall (top), Desk (collapsed in nav header), and CM Card (one-line summary at the foot).

04 — Entity Sentiment Panels

Every number is a doorway, not a headline.

TVK · CM Vijay · AadhavArjuna · all parties. Weighted score always shown beside raw. Tap opens the receipts drawer: the mentions behind the number, reasons on labels, top 3 contributors, what changed.

Entity Panel — All States & Fields

component: EntityPanel

| Field | Source | Display rule |
| --- | --- | --- |
| entity_name | Doctrine Pack config.entities\[id\].display_name | Tamil name if locale ta; English name if locale en |
| weighted_score | Computed: Σ(Ia × N × M × E) normalised | Shown as −41% / +34% split (neg left, pos right). Colour: neg = --t1, pos = --t3, ≤±5% from last = --neutral. Never shown alone — always paired with raw. |
| raw_score | Simple count of negative / positive labels | Toggle between weighted and raw in one tap. Both views always available. |
| denominator | mention count in current window | "of 812 mentions" always visible. If \<30: "not enough data (N of 30 needed)" chip replaces the score. |
| trend_arrow | z-score vs 15-min rolling window | ↑ or ↓ only when z ≥ 2 against rolling baseline; otherwise flat line. Never decorative. |
| velocity | Change in negative mentions per 15 min | "+12% in 30 min" in --t1 when rising; suppressed when flat |
| top_contributor | Account with highest single-item Ia score | "Top: @handle (Ia: 0.87)" — one click to that mention's receipt |
| cap_note | Single-account post-cap (default 5%) | When top contributor accounts for ≥ 5% of score: "1 account driving 5% cap" chip shown |
| last_updated | stage_timestamps.display on latest processed item | "upd HH:MM" in monospace. If stale: "data delayed since HH:MM · X" in amber |
| window | config.sentiment_window (default: last 60 min) | Shown as "last 60 min" in tooltip; can be switched to 15m / 6h / 24h from desk |

Receipts Drawer — opened by tapping any number

component: ReceiptsDrawer

TVK · Negative mentions · 11:00–11:43 · 338 of 812 weighted | raw ⇄ · export CSV

@natnl_jrnlst Ia 0.90 · T1 Negative verified · 1.2M · national media · attack=true · named source

@state_news_tn Ia 0.72 · T2 Negative state media · article · anonymous insinuation

@sarcasm_post_01 Ia 0.08 · T3 Positive sarcasm sarcastic support · effective polarity = positive. Corrected by Meena

| Receipt field | Always shown | Notes |
| --- | --- | --- |
| author / outlet | Yes | Hidden in presentation-safe mode if not public actor |
| engagement | Yes | Reposts / likes / replies at time of labelling |
| posted_at | Yes | IST HH:MM:SS in monospace |
| label + reason | Yes | Every label shows reason text; sarcasm shown as separate flag |
| confidence | Yes | 0–1 shown as Low/Medium/High pill |
| corrected_by | When corrected | "Corrected by \[name\] · original: Negative · reason: sarcastic support" |
| original link | Yes | Direct URL to original post/article |
| model_version | Tooltip | For reproducibility audit |

05 — Alerts Rail

Ranked by severity. Max 3 on the wall. Related grouped.

The wall shows at most 3 alerts. T1 flashes and goes to WhatsApp. T2 stays in-app. T3 is feed only. Anyone can acknowledge — it clears for everyone with "taken by \[name\]".

Alert Card — Fields & Behaviour

component: AlertCard

| Field | T1 | T2 | T3 |
| --- | --- | --- | --- |
| tier badge | T1 · red | T2 · amber | T3 · green |
| title | AI-written, 1 line, reason | AI-written, 1 line | AI-written, 1 line |
| explanation | 2 lines, expanded | 2 lines, collapsed | 1 line |
| evidence tap | Always visible | Always visible | Tap to expand |
| Ack button | Prominent | Present | Present |
| Assign button | Present | Present | — |
| Wall flash | Border pulse | No | No |
| WhatsApp | Sent to leads | No | No |

Severity Formula

§4b Detection Spec

\# Item severity S_item = Ia × N × max(M, M_floor) × E # Story severity (noisy-or over top-k items) S_story = 1 − PRODUCT(1 − S_item) # capped at 1 # Tiers (default; versioned in Doctrine Pack) T1: S_story ≥ 0.40 → wall flash + WhatsApp T2: S_story 0.20–0.40 → in-app only T3: S_story \<0.20 → feed only # Sanity check examples # Nat'l journalist (Ia 0.90, N 0.90, M 0.60, E 1.0) = 0.49 → T1 # 40-follower account (Ia 0.10, N 0.90, M 0.20) = 0.02 → T3

Alert Lifecycle States

component: AlertCard · all states

Firing

Border pulse (T1). AI explanation streaming ▌. WhatsApp queued.

Acknowledged

Flash clears for everyone. "Taken by Meena · 11:44". Remains on wall for context.

Assigned

Assigned to \[name\] shown. Owners see it in their queue on the desk.

Grouped

Related posts merged under one alert. Tap to expand. Never floods.

Cleared

Removed from wall after close. Visible in Alerts history on desk.

WhatsApp fail

"WhatsApp not delivered" shown in-app. Lead notified. Retry queued.

Influence Score Formula

§3 Detection Spec

Ia = clamp( 0.30×F + 0.20×V + 0.35×T + 0.15×C ) × A F = min(1, log10(1 + followers) / 7) # 10M followers → 1.0 V = staff "notable/official" flag: 1.0 paid-verified only: 0.3 | none: 0 T = national media 1.00 | party leader 0.90 state media 0.80 | influencer 0.60 local/known 0.40 | unknown 0.10 A = authenticity 0.30–1.00 (bot suspicion down-weights) # All weights versioned; every change audit-logged

06 — The Desk (Web App)

Every page, every component, every AI interaction.

The desk is the analyst's home. Keyboard-first. Dense tables. Every number a doorway. Split panes. Bilingual. 13 navigation items, each a distinct surface with its own AI role.

Now

Stories

Alerts

Response Queue

Allies

Campaigns

Tone Lab

Clippings

Ask

Settings

Time Machine

Admin

Page Specifications — All 13 Nav Items

desk navigation

| Page | Primary components | AI role on this page | Key interactions |
| --- | --- | --- | --- |
| Now | Wall picture (same layout as wall, slightly smaller), My Assignments rail, Open Alerts list, Pulse Strip expanded | AI explanation chip on each active alert; streaming updates to open items; co-worker mini-panel bottom-right | Ack / assign alerts; tap any number for receipts; switch to presentation-safe |
| Stories | Kanban board: columns Open Window / Closing / Closed / Unattributed. Each card: Mileage Clock chip, reach split, severity chip, TVK status dot, owner avatar | AI clusters Mentions into stories; AI predicts window close time; AI writes story title from top Mentions | Filter by entity / source / date; merge stories; split story; bulk assign; sort by window closing |
| Story Page | Header (title, clock state, severity, owner), Reaction Roster, Origin Trace tree, Receipts list, Co-worker panel, Actions bar | AI populates Reaction Roster from expected reactors; AI writes 2-line explanation; AI suggests response kind; AI detects TVK response | Acknowledge; assign; mark acted / not needed; merge/split; correct a label; ask co-worker follow-up |
| Alerts | Alert list (all tiers); filter by tier / entity / status; detail drawer with full evidence | AI groups related alerts; AI writes reason per alert; AI flags when alert dismissed-rate is high | Filter; ack; assign; drill to story; export |
| Response Queue | Queue sorted by window-closing-soonest; each item: suggested response kind, roster summary, TVK status, Mileage Clock state | AI suggests response kind (condemn / rebut / congratulate / condole / claim-credit); AI detects TVK response; AI records outcome in Mileage Ledger | Mark acted (with time-to-response recorded); mark not needed (with reason); drill to story |
| Allies | Ally Mood seismograph per ally; per-issue breakdown; tremor history; people + posts per ally | AI detects tremor per figure against 30-day baseline; AI flags when ≥2 ally figures tremor within 6h; AI holds CM-blaming text for lead review | Drill to ally public figure; view posts; mark tremor as noise; override threshold |
| Campaigns | Network constellation view; timing strip; organic-ness index; evidence pack; attribution panel (locked until lead confirms) | AI runs coordination detection; AI computes organic-ness index; AI writes evidence pack; AI never names a party — human must confirm | View cluster; expand evidence; confirm attribution (lead only); mark as noise; track spread timeline |
| Tone Lab | Lexicon list with examples; label queue of low-confidence items; per-language accuracy chart; sarcasm cases; "how I read this" panel | AI shows reasoning per label; AI flags when correction rate rises for a capability; AI tracks week-on-week accuracy | Add lexicon term with examples; correct label inline; add sarcasm example; approve/reject AI suggestion |
| Clippings | Upload dropzone (batch drag-and-drop); processing status per clipping; "check scan" queue; page preview; OCR text with confidence | AI runs Tamil OCR; AI marks low-confidence text as "check scan"; AI links clipping to existing Story if entity + topic match | Upload batch; approve / correct OCR text; link to story; mark edition checked |
| Ask | Chat interface; language auto-switches; citation chips on every claim; evidence panel alongside answers; confidence indicator | AI answers in Tamil or English; cites Mentions by ID; says "not enough data" when true; never invents numbers; first answer ≤10s | Ask in Tamil or English; tap citation to expand source; mark answer useful/not-useful; drill to story from answer |
| Settings | Doctrine Pack data with version history and diff view; audit log; threshold editor; entity/relationship editor; tier editor; recipient config | AI shows diff when a setting is changed; AI flags when a threshold change would have altered past alert outcomes (via Time Machine link) | Edit watch terms; change relationship tag; update tiers; configure recipients/quiet hours; review version history |
| Time Machine | Window picker; threshold slider; replay run launcher; "would have alerted" vs actual comparison; DRILL launcher | AI re-runs the full pipeline on historical raw items with chosen thresholds; AI flags discrepancies vs actual alerts; AI tags DRILL events | Pick window; adjust thresholds; run replay; view must-pass scenario results; launch drill |
| Admin | Source health cards; crawler health; X usage vs cap (50/80/95% alert); missed-item audit; licence report cards; correction/bias dashboard | AI summarises filter-recall sample weekly; AI flags when correction rate by person exceeds bias threshold; AI reports licence stage per capability | View source health; resolve crawler issue; review usage; run filter audit; promote/demote licence stage; view bias dashboard |

07 — Reaction Roster + Mileage Clock

Silence is a first-class signal. The roster makes it visible.

For every Story, a roster of expected reactors — allies, opposition, media, TVK leaders — shown as Spoken / Silent / Not yet. "Others are condemning, TVK silent" is a visible state, not something a human must notice.

Reaction Roster Component

component: ReactionRoster

Story: Councillor remark at DMK function · T+00:23

Congress MLA

ally · spoken

@natnl_jrnlst

nat'l media · spoken

TVK Leader

NO RESPONSE YET

VCK Official

ally · silent

State Media

silent

Opp. Leader

opposition · spoken

🐘 "No TVK leader post detected. Checking registry of 12 TVK leader accounts."

Medium confidence · registry match

| Roster Status | Display | Detection method |
| --- | --- | --- |
| Spoken ✓ | Green dot + "spoken" | Post from actor's account matching story semantically (embedding sim ≥ τ=0.82) or explicit quote/reply to story source |
| Silent ○ | Grey dot + "silent" | Actor in expected-reactors roster; no post detected in story window |
| Not yet · | Amber pulsing dot + "NO RESPONSE YET" | TVK leader accounts specifically; response opportunity is open |
| Responded elsewhere | Blue dot + "press meet / TV" | Manual "responded elsewhere" button pressed by lead; stops alerting |

Mileage Clock Component

component: MileageClock

Mileage Clock · Response Window

OPEN

Predicted peak: 18–35 min "early estimate"

| Clock State | Condition | Colour | Action |
| --- | --- | --- | --- |
| OPEN | velocity rising OR within 80% of peak | --t3 green | Response opportunity live |
| CLOSING | velocity \< 50% of peak | --t2 amber | Urgency indicator; "window closing soonest" sort |
| CLOSED | velocity \< 15% of peak OR age > 24h | --neutral grey | No more response opportunity; ledger records outcome |

Predicted peak: fitted from similar past Stories (type, source tier, hour). Shown as a band, never a point. Labelled "early estimate" when fewer than N similar Stories exist. Cold start uses pooled heuristics.

Response Opportunity Trigger

§6 Detection Spec

\# Reaction score (last 60 min) R = Σ weight(reactor_tier) over DISTINCT reactors national media: 1.0 | state media: 0.8 ally public figure: 1.0 | other-party leader: 0.8 recognised figure: 0.6 | influencer: 0.4 # Trigger fires when ALL hold candidate=true # attack or positive trigger detected AND R ≥ 2.0 # reaction threshold (default) AND tvk_response == none AND clock != Closed # Alert: WhatsApp to Sajan ≤ 5 min from trigger

Story Page — Full Component Map

Councillor remarks ill of CM at DMK function; deputy general secretary on stage

T1

Started **11:00**

Reach: **9 outlets · 1,200 posts**

Clock: **OPEN \~18 min**

Severity: **0.72 (T1)**

Owner: **Meena**

● Live

Reaction Roster

● 3 spoken · ○ 2 silent · · TVK: no response

Origin Trace

Event video → @local_acct (11:00) → YouTube news (11:12) → 2 news sites (11:15) → X spread

Story Page — All Component Zones

page: /stories/\[id\]

| Zone | Contents | AI fills | Human actions |
| --- | --- | --- | --- |
| Header | Title, started-at, clock state chip, severity chip, owner avatar, story status (open/closed) | AI writes title from top Mentions; AI assigns severity | Rename story; assign owner; merge/split |
| Reaction Roster | Expected reactors by tier, each with spoken/silent/not-yet status and timestamp | AI detects reactions from X and news; AI detects TVK leader response from registry | Add manual "responded elsewhere"; configure expected reactors in Settings |
| Origin Trace | Propagation tree: first seen → spread path across platforms; "caught first" flag; publisher breakdown | AI builds tree from stage_timestamps and semantic links; AI flags "print first" | Correct a link in the tree; mark a node as source |
| Receipts | All Mentions: author/outlet, engagement, label, reason, original link, corrected-by marks; sorted by influence desc | AI labels each Mention; AI writes reason; AI flags low-confidence for human review | Correct any label; add a Mention manually; link a clipping |
| Co-worker panel | 2-line explanation of the Story; suggested response kind; "ask a follow-up" input; citation chips | AI writes explanation grounded in Mentions; AI suggests kind (condemn/rebut/etc); AI answers follow-ups | Ask follow-up in Tamil or English; mark explanation useful/not-useful |
| Actions bar | Acknowledge; Assign; Merge/Split; Mark acted / not needed; "responded elsewhere" | AI records outcome in Mileage Ledger | All action buttons here; outcome choice feeds the response queue learning |

08 — Constellation / Campaign View

Network of coordination. Organic-ness index. Attribution gate.

Coordinated campaigns shown as a network constellation: nodes sized by influence, ringed by account age, edges for shared text / mutual boosting. The question shifts from "is it suspicious?" to "how far from organic?"

possible coordination

confidence: Medium

\~300 accounts · 70% \<3 months

not attributed · awaiting lead confirmation

new account (\<90d)

old account, clustering

organic baseline

node size = influence · ring = account age

Organic-ness Index 0.21 / 1.0

Far from organic · low text diversity · tight timing · 70% new accounts

Campaign Signals

§8 Detection Spec

| Signal | Method | Shown to user |
| --- | --- | --- |
| Account-age skew | Share of accounts \<90d vs organic baseline for that hashtag size | % bar + number |
| Text near-duplication | MinHash / embedding cosine ≥ 0.92 clusters | % share in top 3 phrasings |
| Timing synchrony | Share of posts in same 60-second bins | Burst vs diurnal chart |
| Mutual boosting | Density of reciprocal repost/reply edges | Edge density score |
| Low-history / similarity | Default avatars, handle patterns, empty history, same client app | Profile similarity score |
| Network smallness | Hashtag volume explained by few accounts relative to total | Coverage ratio |

campaign_score = logistic(b0 + Σ(w_i × signal_i)) # weights fitted on labelled examples, versioned confidence = High (≥3 independent signals) | Medium | Low min cluster: 30 accounts # below = observe only

Attribution Gate

hard requirement · tested at API level

Default display

"Possible coordination · confidence Medium · not attributed"

Attribution locked

UI shows lock icon. Neither the UI nor the co-worker can name a party. Tested at API level.

Confirmation required

An attribution record (party, evidence, confirming lead name, reason, timestamp) must exist in the DB before any surface can display a party name.

After confirmation

"Attributed to \[Party\] · confirmed by \[Name\] at HH:MM · evidence: \[link\]"

Privacy: evidence shows account handles for public actors and clusters of accounts as accounts. No private-citizen profile pages. No cross-linking to petition or citizen records. Presentation-safe mode hides handles.

09 — Tone Lab + Co-worker Panel

Tamil-native. Team-taught. Every correction kept and audited.

The Tone Lab is where the team curates Tanglish idioms, sarcasm patterns and nicknames. The system shows "how I read this" with the reason. Corrections land here, feed the gold set, and are tracked per language for bias.

Label Correction Flow

component: LabelCorrector

Tanglish · 15:30 · @user_xyz\
"Enna periya leader nu ninaikkiraar, aana CM ku oru tweet kooda poda maataar" 😂

Negative sarcasm

✏️

🐘 How I read this:

This post mocks a leader's silence. The sarcasm ("enna periya leader") means the writer does NOT support the entity. Effective polarity: **Negative**. Sarcasm flag = true (for audit). Claim backing: **satire / satire (implicit)**.

High · 0.87

✓ Meena corrected · 15:30\
Changed: Negative → **Positive** (sarcastic support). Reason: "sarcasm reads as mockery of silence = effective support implied". Original label kept in history.

Corrected

Label Schema — All Fields

§2 Detection Spec

| Field | Values | Notes |
| --- | --- | --- |
| polarity | positive \| negative \| neutral \| mixed | Effective polarity — sarcasm already applied by the model |
| sarcasm | bool | Separate from polarity; for audit, analytics and Tone Lab only |
| attack | bool | Directed attack on the entity; lifts N score |
| claim_backing | on_record \| named_source \| anonymous_insinuation \| satire \| none | Affects N score weight; shown to users |
| language | ta \| en \| tanglish \| mixed | Auto-detected; X language tag not trusted for Tanglish |
| reason | Short text | Always shown to users; human must write for corrections |
| confidence | 0–1 | Shown as Low/Medium/High pill |
| model_version | string | For reproducibility; visible in receipt tooltip |

🐘

Naadi Co-worker

answering in Tamil · citing 3 Mentions

Congress MLA-களின் X posts பார்க்கும்போது, cabinet decision பற்றிய negative sentiment இன்று 09:00–12:10 இடையில் 34% அதிகரித்திருக்கிறது\[1\]. மூன்று MLA posts ஐ based ஆக சொல்கிறேன், sample size 64 posts மட்டுமே, இதை கவனத்தில் வைக்கவும்\[2\].

▸ How I read this tap to expand

Ask a follow-up

👎 not useful

Co-worker Safety Design

§5 Architecture

| Rule | Implementation |
| --- | --- |
| All content is data, never instructions | Ingested posts wrapped in delimiters; "ignore your instructions" treated as a post |
| Numbers from tool results only | Validator rejects uncited claims; every number carries a tool call ID |
| No outbound social tools | Tool list: read-only query tools + send_internal_alert (allowlisted recipients only). No outbound HTTP. |
| Injection test suite in CI | Posts with hidden text, poisoned articles, "ignore" prompts; must all pass as data items |
| Ally-blame held | Any co-worker text blaming an ally held for lead review before CM Card delivery |
| Uncertainty is explicit | "I can't say from the data we have. I found N posts; here's what they show." — mandatory phrasing |

Bias Guard

§H PRD — correction bias prevention

| Trigger | Gate |
| --- | --- |
| Correction softens negative label about our entities | Requires second reviewer before changing aggregates |
| Correction touches a high-severity Story | Requires second reviewer |
| Model-only view | Always available alongside corrected view; never hidden |
| Correction rate per person | Tracked and shown in Admin bias dashboard; flag if one person's rate diverges |

10 — Pocket: WhatsApp + CM Card

The lead gets one alert. They must act in 90 seconds.

WhatsApp templates delivered to named individuals (never groups). Quiet hours respected. Delivery receipt required. Failed delivery escalates to next lead and shows in-app. All template text to be confirmed with the war room.

📱

🐘 Naadi

11:28

**🐘 Response opportunity · OPEN window**

Kind: **condemn** · CM Vijay

What: Councillor ill remark at DMK function, DGS on stage

↗ clip.link/abc123 · video clip \


Already reacting:

· @natnl_jrnlst (nat'l media, 1.2M)

· Congress MLA Murugesan (ally)

· Opp. leader @aiadmk_official

TVK response: **none yet**

Started: 23 min ago · Clock: OPEN

\
↗ Evidence: naadi.app/story/abc · tap to open

✓✓ delivered 11:28

WhatsApp Template Spec

Official Business API · approved templates

| Template | Recipient | Required fields | Delivery rule |
| --- | --- | --- | --- |
| Response opportunity | Sajan (named lead) | Kind, entity, 1-line summary, clip/article link, reactor list (≤3), TVK status, clock state, evidence link | ≤5 min from trigger; individuals not groups; retry once then in-app escalation |
| High influence trigger | All leads | Handle/outlet, tier, followers, N reposts in M min, reason, evidence link | Immediate on T1; individuals not groups |
| Dawn Brief | Leads per routing config | 3–5 lines: overnight summary, any campaign, open items, what to watch. One link. | 06:30 IST daily; quiet hours respected per recipient |
| Dusk Baton | Outgoing + incoming shift lead | Open alerts, owners, watch items for overnight | At shift end time per config |

Failed delivery: retry once, then in-app "WhatsApp not delivered" badge on the alert, next lead notified in-app. All receipts logged in delivery_log table.

CM Card — 30-second read

component: CMCard

2026-11-04 · CM Card · changes since 09:00

1\. Councillor remark story — now T1

Cause: Rival-party councillor remarks at DMK function; 3 allies and national media already condemned; no TVK response detected.

High confidence

→ evidence

2\. Congress ally — negative trend

Cause: 2 Congress MLAs posted against cabinet decision since 09:00. Reviewed by Meena.

Low confidence · 18 posts

NEEDS A DECISION

· Respond to councillor remark? Window: OPEN \~18 min.

English

தமிழ்

| CM Card rule | Spec |
| --- | --- |
| Max changes | 3. If more qualify, the top 3 by severity shown; a note says "+N more in desk" |
| Change format | 1-line headline + 1-line cause + confidence chip + 1-tap to evidence. Never more. |
| Ally-blaming items | Show "reviewed by \[name\]" or held until a lead reviews. Never reaches CM unreviewed. |
| Language switch | English ↔ தமிழ் toggle; large type (18px+ body); no raw private handles |
| Presentation-safe | CM Card is always presentation-safe by default |

11 — Time Machine

Replay history. Prove thresholds. Run drills.

The Time Machine re-runs the pipeline on historical raw items with any chosen thresholds. The councillor incident and the one-post-that-mattered are replay fixtures that run on every release. Drill mode injects synthetic events to measure team reaction time.

Must-Pass Scenario A: The Councillor Incident

replay fixture · runs on every release

T+00:00 EXPECT: WhatsApp to Sajan ≤ T+00:28 T+01:00

event

clip posted

R≥2.0 trigger

WhatsApp sent

TVK response

00:00 · Event 00:07 · Clip posted 00:22 · R≥2.0 00:23 · Trigger 00:28 · WA ✓ 00:40 · TVK reply

| Assert | Pass condition |
| --- | --- |
| (a) Alert latency | WhatsApp to Sajan ≤ 5 min from trigger (T+00:28 latest) |
| (b) Content fields | Clip link, reactor list (≥3 names/tiers), "no TVK response yet", clock state = OPEN all present |
| (c) No repeat alert | After TVK leader reply detected at T+00:40, no further WhatsApp fires |
| (d) Wall display | Story shows as T1/T2 per computed severity on the wall |
| (e) Mileage Ledger | time_to_response recorded; outcome = "responded by \[name\] at 00:40" |

Must-Pass Scenario B: One Post That Mattered

replay fixture · runs on every release

| Assert | Pass condition |
| --- | --- |
| T1 fire | Wall flash and WhatsApp at or before T+00:05 (influence floor path; M_floor=0.5 when Ia ≥ 0.80) |
| Reason shown | "verified, 1.2M followers, national media, N reposts in M min" in alert |
| No false positive | 40-follower account with identical text does NOT alert |
| Ack clears | Flash clears for all users on acknowledge; "taken by \[name\]" shown |

Negative fixtures (must NOT alert): film-fan "Vijay" chatter; sarcastic supportive Tanglish post; legitimate organic hashtag with diverse accounts.

Time Machine UI Components

page: /time-machine

| Component | Spec |
| --- | --- |
| Window picker | Date range selector; min 2026-05-10 (history start); backfill status per period shown |
| Threshold sliders | All Doctrine Pack defaults shown with sliders; changed values highlighted orange |
| Run replay button | Launches replay in sandbox namespace; real data, real pipeline code, virtual clock; outputs never go to real surfaces |
| Would-have-alerted view | Side-by-side: "would have fired" vs "actually fired"; diff highlighted; drill to individual item |
| DRILL launcher | Inject a labelled synthetic event; alert goes only to drill recipient list; never to real recipients. Drill alerts tagged DRILL everywhere. |

12 — Licence Ladder + Admin

Trust is earned and measured. The admin sees everything.

Each capability (labels per language, spike alerts, campaign flags, response suggestions, briefs) has a licence that is earned through measured accuracy and suspended automatically when errors rise. The Admin page shows every source, every cap, every correction, every capability stage.

Probation / Assist

Suggests labels, alerts and responses. Human reviews most output. Every correction teaches it.

Licensed / Trusted

Acts alone on capabilities that cleared accuracy bar (≥14 days, ≥200 reviewed items). Human spot-checks samples.

Co-worker / Autonomous

Handles routine monitoring, labelling, alerting and briefing end-to-end. Raises only decisions.

Licence Report Card

component: LicenceCard · Admin page

| Capability | Language | Stage | F1 / bar | Days at stage |
| --- | --- | --- | --- | --- |
| Sentiment labels | English | Trusted | 0.84 / 0.80 | 21 |
| Sentiment labels | Tamil | Co-worker | 0.78 / 0.75 | 8 |
| Sentiment labels | Tanglish | Probation | 0.68 / 0.72 | 3 |
| Spike alerts | All | Trusted | precision 0.73 / 0.70 | 14 |
| Campaign flags | All | Probation | precision 0.61 / 0.70 | 2 — AUTO-DEMOTED |
| Response suggestions | All | Probation | — | Not yet calibrated |
| Briefs | Tamil/EN | Trusted | eval 0.88 / 0.80 | 7 |

Auto-demotion fires when accuracy falls below bar − 0.03 for 3 consecutive days, or one high-severity error. Leads see reason: "Campaign flags back on Probation: precision fell to 0.61 vs bar 0.70".

Admin Page Components

page: /admin

| Component | Shows | AI role |
| --- | --- | --- |
| Source health cards | Per source: last OK, lag, error reason. Never "no new items" — always a specific error. | AI flags when source health degrades vs 7-day baseline |
| X usage meter | Reads used vs monthly cap; alert at 50/80/95%. Sampling labelled on affected numbers. | AI projects when cap will be hit at current rate |
| Missed-item audit | Weekly: sample of rejected items re-labelled; missed-item rate by source/language/entity | AI summarises audit; flags rising missed-item rates |
| Licence report cards | All capabilities × languages; F1 trend, error trend, days at stage, bar | AI triggers promotion/demotion; writes reason in plain language |
| Correction/bias dashboard | Corrections per 1,000 labels per person/entity; softening-correction rate; second-review queue | AI flags diverging correction rates; bias guard triggers |

13 — AI-Native Interaction Patterns

Every surface state. Every AI moment. Every edge case.

Universal Empty / Stale / Error States

applies to all surfaces

Empty — no mentions

"No Mentions in the last 15 minutes. Source health: X ✓ News ✓" — never just blank

Stale source

"Data delayed since 10:07 · X" — in amber on affected panel; Pulse Strip trace goes flat red

Low sample

"Not enough data (18 of 30 needed)" — chip replaces percentage; never zero

AI down

"AI down · rules-only alerts active · co-worker data may be stale" — dashboards keep working

Corrected label

"Corrected by Meena · original: Negative · reason: sarcastic support"

Coordination

"Possible coordination · confidence Medium · not attributed" — until lead confirms

Co-worker uncertainty

"I can't say from the data we have. I found 12 posts; here's what they show."

Sampled data

"\~sampled" badge on every affected number; tooltip says what percentage was sampled

Cap approaching

Admin + Pulse Strip: "X reads at 80% of monthly cap" — at 50/80/95%

Significance

Trend arrows only when z≥2. Otherwise flat. Never implied trend without test.

Early estimate

"Early estimate — fewer than N similar stories" label on predicted peak band

Drill mode

All drill alerts tagged DRILL in red. Go only to drill recipient list. Never to real recipients.

Accessibility Requirements

WCAG AA · Tamil line-height · keyboard-first

| Requirement | Implementation |
| --- | --- |
| Keyboard navigation | Full tab order on desk; skip-to-content link; focus rings visible (not just default blue — use --pulse colour at 2px) |
| Screen reader labels | All icon buttons have aria-label; all status dots have aria-label text ("T1 alert · Response Opportunity"); live regions for counter updates |
| Colour contrast | WCAG AA on desk (4.5:1 body, 3:1 large text); AAA targets on wall (7:1 body); never colour alone |
| Tamil line-height | Tamil body text: line-height 1.8 minimum; Noto Sans Tamil ascenders tested at 14px in dense tables |
| Tanglish rendering | lang attribute switches mid-line; no font substitution breaks |
| Reduced motion | @media (prefers-reduced-motion): all animations skip to end-state; Pulse Strip shows as static bar; T1 flash shows as solid border immediately |
| Focus management | Drawer open: focus moves to first interactive element inside; drawer close: focus returns to trigger |
| Touch targets | Minimum 44×44px on mobile web; CM Card buttons 48×48px |

Doctrine Pack — What It Configures

zero TVK-specific values in code

| Setting | TVK default | Example alternative |
| --- | --- | --- |
| entities\[\] | TVK, CM Vijay, AadhavArjuna, all parties | Company, CEO, Competitors (business client) |
| entity_relations\[\].tag | ally \| supporting \| opposition \| neutral | partner \| competitor \| regulator \| neutral |
| vocabulary.cm_label | "CM summary" | "Leadership summary" |
| vocabulary.party | "party" | "brand" |
| co_worker.name | TBD (Thunai / Yaanai — client's call) | Configurable per client |
| co_worker.avatar | Official TVK elephant artwork | Client-supplied artwork |
| theme.accent | #E8291E (TVK red) | Client brand colour |
| alert_recipients\[\] | Sajan (response opp.), leads (influence) | Per-client routing config |
| thresholds.r_min | 2.0 | Calibration sets per client |
| watch_terms\[\] | TVK names, nicknames, Tamil/Tanglish spellings | Client-specific terms |
| lexicon\[\] | Tamil/Tanglish idioms, sarcasm patterns | Client's industry language |
| reaction_rosters\[\] | Allies, opposition, media expected reactors per story type | Competitors, regulators, analysts |

A new client = a new Doctrine Pack + a gold-set sprint. No code changes. Each client's data, corrections, gold set and co-worker memory are kept fully separate. One client's corrections never change another client's labels.

🐘

Naadi is the war room's pulse.

It watches X, news, TV, e-paper and clippings in Tamil, English and Tanglish; weighs who is speaking; fuses it into Stories; tells you how long the window to respond stays open and who has already spoken; spots coordinated pushes; and shows the evidence behind every number. It never posts. It makes sure TVK is never the last to know.

Naadi UI & Component Deep Spec · v1.0 · 2026-11-04 · Minsky Product