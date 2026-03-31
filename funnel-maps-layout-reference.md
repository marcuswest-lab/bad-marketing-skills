# Funnel Maps - Layout Reference
All labels match the exact metric names from each client's reporting dashboard.

---

## 1. TRUSY - Low Ticket Funnel

**Frame Title:** Low Ticket Funnel
**Header:** Trusy - Low Ticket Funnel
**Last Checked For Accuracy:** 3/27/26
**Checked by:** Marcus

### Funnel Flow (left to right):

```
[Ads] ──→ [Landing Page] ──→ [Checkout Page] ──→ [Purchase]
```

### Step Details:

| Step | Shape | Key Outcome | Arrival Count | Completion Count |
|------|-------|-------------|---------------|-----------------|
| Ads | Rectangle (blue) | Clicks | Impressions | Unique Outbound Clicks |
| Landing Page | Rectangle (green) | Initiate Checkout | Unique Outbound Clicks | Initiate Checkouts |
| Checkout Page | Rectangle (purple) | Sale | Initiate Checkouts | Sales |

### Rate Metrics (on connector lines between steps):
- Ads → Landing Page: **CTR (UOC)**
- Landing Page → Checkout: **Checkout Rate**
- Checkout → Purchase: **Purchase Rate**

### Cost Metrics (text labels between steps):
- After Ads: **CPM**, **CPC (UOC)**
- After Landing Page: **CPIC**
- After Checkout: **CPA/CAC**

### URLs:
- LP: trusy.co/pages/trusy-landing-1
- Checkout: trusy.co/checkouts/...
- Events: Initiate Checkout = Shopify event, Sale = Shopify purchase event

### Notes:
- No AOV/Revenue/ROAS tracking
- No decision diamonds needed — straight linear funnel

---

## 2. CEO LAWYER PI - Lead Gen Funnel

**Frame Title:** Lead Gen Funnel
**Header:** CEO Lawyer PI - Lead Gen Funnel
**Last Checked For Accuracy:** 3/27/26
**Checked by:** Marcus

### Funnel Flow:

```
[Ads (EN/ES/AR)] ──→ [Landing Page] ──→ [Form (10 Qs)] ──→ ◇ Is Qualified? ◇
                                                                  │           │
                                                                 Yes         No
                                                                  │           │
                                                                  ▼           ▼
                                                          [Qualified TY]  [DQ TY Page]
                                                          (MQL/3+ Lead)
                                                                  │
                                                                  ▼
                                                        [SMS/Email Follow-up]
                                                                  │
                                                                  ▼
                                                      ◇ Is Wanted Lead? ◇
                                                           │          │
                                                          Yes        No
                                                           │
                                                           ▼
                                                     [Signed Case]
```

### Step Details:

| Step | Shape | Key Outcome | Arrival Count | Completion Count |
|------|-------|-------------|---------------|-----------------|
| Ads (EN/ES/AR) | Rectangle (blue) | Clicks | Impressions | Clicks |
| Landing Page | Rectangle (green) | Lead (opt-in) | Clicks | Leads |
| Qualification | Diamond | MQL or DQ | Leads | 3+ Leads (MQLs) |
| SMS/Email Follow-up | Rectangle (purple) | Wanted Lead | 3+ Leads | Wanted Leads |
| Signed Case | Terminator | Sale | Wanted Leads | Signed Cases |

### Qualification Logic (for diamond):
- **Truck/Trailer:** Always qualified (no DQ rules)
- **All others, DQ if:**
  - 2+ years ago
  - 1+ month ago AND no medical treatment
  - Work injury
  - 1+ year ago AND already has attorney
- **Qualified → meta.ceolawyer.com/thankyou** (fires "Submit Application" in GTM)
- **DQ → ceolawyer.ceolawyer.com/meta_uq_landing_page/**

### Form Questions (10-step):
1. Accident type (Car/Motorcycle/Truck/Slip&Fall/Work Injury/Other)
2. When was accident (Within 7 days/1-4 Weeks/1-12 Months/1-2 Years/2+ Years)
3. Medical treatment (Yes/No)
4. Injury severity (1-5 scale)
5. Who was at fault (Me/Someone else)
6. Working with attorney (Yes/No)
7-10. First Name, Last Name, Email, Phone

### Rate Metrics (on connector lines):
- Ads → LP: **CTR (UOC)**
- LP → Qualification: **Opt-in CVR**
- Qualified rate: **3+ %**
- Wanted rate: **% Wanted**
- Close rate: **% Lead To Close**

### Cost Metrics:
- **CPM**, **CPC (UOC)**, **CPL**, **CP3L**, **CPWL**, **CPA**

### Landing Pages:
- English: meta.ceolawyer.com/find-out-how-much-your-case-is-worth
- Spanish: ceolawyer.ceolawyer.com/phase-2-tof-cbo-lead-spanish-april2025
- Arabic: meta.ceolawyer.com/ar/find-out-how-much-your-case-is-worth

### Wanted Lead (SQL):
- Signed case OR pending signed case

---

## 3. NURSE COACH - Two Funnels (1 Board, 2 Frames)

### FRAME 1: Free Training Funnel

**Frame Title:** Free Training Funnel
**Header:** Nurse Coach - Free Training Funnel
**Last Checked For Accuracy:** 3/27/26

### Funnel Flow:

```
[Ads (Meta+Google)] ──→ [Opt-in Page] ──→ [Free Training/VSL] ──→ [Typeform Application] ──→ ◇ Is Nurse? ◇
                                                                                                 │         │
                                                                                                Yes       No (D)
                                                                                                 │         │
                                                                                                 ▼         ▼
                                                                                         [Schedule Call]  [DQ]
                                                                                         (Acuity Booking)
                                                                                                 │
                                                                                                 ▼
                                                                                              [Sale]
```

### Step Details:

| Step | Shape | Key Outcome | Arrival Count | Completion Count |
|------|-------|-------------|---------------|-----------------|
| Ads | Rectangle | Clicks | Impressions | Clicks |
| Opt-in Page | Rectangle | New Lead | Clicks | New Leads |
| Free Training | Rectangle | Watch Training | New Leads | — |
| Typeform App | Rectangle | Qualified Call | — | Qualified Calls |
| Schedule Call | Rectangle | Registration | Qualified Calls | Registrations |
| Sale | Terminator | Sale | Registrations | Sales |

### Qualification (Diamond):
- Q1: "Choose what best describes you"
  - A: RN, license active → Qualified
  - B: RN, license NOT active → Qualified
  - C: LPN/LVN → Qualified
  - D: NOT a Nurse → DQ

### Rate Metrics:
- **CTR (UOC)**, **Opt-in CVR**, **Qualified %**, **Event Opt-in %**, **Close Rate**

### Cost Metrics:
- **CPM**, **CPC (UOC)**, **CPL**, **CPQC**, **CP Reg**, **CPA/CAC**

### URLs:
- Opt-in: l.thenursecoaches.com/free-training
- Training: thenursecoaches.com/free-training/
- Application: thenursecoaches.com/apply-now/
- Schedule: thenursecoaches.com/schedule-call/?step=enroll

---

### FRAME 2: Live Event Funnel

**Frame Title:** Live Event Funnel
**Header:** Nurse Coach - Live Event Funnel

### Funnel Flow:

```
[Ads] ──→ [Registration Page] ──→ [Confirmation Page] ──→ [Live Event / Replay] ──→ [Sale]
                  │
          (Qualification via
           Acuity widget)
```

### Step Details:

| Step | Shape | Key Outcome | Arrival Count | Completion Count |
|------|-------|-------------|---------------|-----------------|
| Ads | Rectangle | Clicks | Impressions | Clicks |
| Registration | Rectangle | Registration | Clicks | Registrations |
| Confirmation | Rectangle | Confirmed | Registrations | — |
| Live Event/Replay | Rectangle | Attend | — | Event Attendees |
| Sale | Terminator | Sale | Event Attendees | Sales |

### Rate Metrics:
- **CTR**, **Registration Rate**, **Attendance Rate**, **Close Rate**

### Cost Metrics:
- **CPM**, **CPC**, **CP Reg**, **CPA/CAC**

### URLs:
- Registration: thenursecoaches.com/live-event
- Confirmation: thenursecoaches.com/live-event-confirmation/
- Replay: thenursecoaches.com/live-event-replay/

---

## 4. CASE SOURCE - Lead Gen → Legal Cases

**Frame Title:** PI Lead Gen Funnel
**Header:** Case Source - PI Lead Gen Funnel
**Last Checked For Accuracy:** 3/27/26

### Funnel Flow:

```
[Ads] ──→ [Landing Page] ──→ [Form (13 Qs)] ──→ ◇ Qualification Logic ◇
                                                      │         │         │
                                                  Qualified   Soft DQ   Hard DQ
                                                      │         │         │
                                                      ▼         ▼         ▼
                                              [Qualified TY] [Unqual TY] [Hard DQ TY]
                                              Lead + QualLead  Lead only   No pixel
                                                      │         │
                                                      ▼         ▼
                                               [Contact Stages (Law Ruler)]
                                                      │
                                                      ▼
                                                   [SQL]
                                                      │
                                                      ▼
                                               [Signed Case]
```

### Step Details:

| Step | Shape | Key Outcome | Arrival Count | Completion Count |
|------|-------|-------------|---------------|-----------------|
| Ads | Rectangle | Clicks | Impressions | Clicks |
| Landing Page | Rectangle | Form Fill | Clicks | Leads |
| Qualification | Diamond (3-way) | MQL or DQ | Leads | MQLs |
| Contact | Rectangle | Contact Made | MQLs | Contacts |
| SQL | Rectangle | Ready to Sign | Contacts | SQLs |
| Signed Case | Terminator | Signed | SQLs | Signed Cases |

### 3-Way Decision Diamond:
**Qualified TY** (fires Lead + Qualified Lead):
- Insured = Yes AND Police report = Yes AND
- (At Fault = Someone else) OR (At Fault = Me AND Medical Treatment = Yes) AND
- Accident: ≤3 months = auto-qualify; 3-24 months = only if Medical Treatment = Yes AND
- Attorney = No

**Soft DQ TY** (fires Lead only):
- Insured = Yes BUT fails other conditions

**Hard DQ TY** (no pixel):
- Insured = No

### Form Questions (13):
1. Accident type  2. When  3. State  4. Insurance  5. Medical treatment
6. Injury severity  7. At fault  8. Police report  9. Attorney
10-13. Name, Email, Phone

### Rate Metrics:
- **CTR (UOC)**, **Form Fill %**, **Marketing Qualified %**, **Contact Rate**, **Sales Qualified %**, **% Lead To Close**

### Cost Metrics:
- **CPM**, **CPC (UOC)**, **CPL**, **CP MQL**, **CP Contact**, **CP SQL**, **CPA**

### URLs:
- LP: topcrashlawyer.com/ (new: go.topcrashlawyer.com)
- Qualified TY: topcrashlawyer.com/thankyou
- Lead TY: topcrashlawyer.com/complete

### CRM: Law Ruler
- Contact statuses: Call back, Sent to firm, Contacted, New Lead, Intake Under Review, etc.
- SQL statuses: Sent e-sign, Signed e-sign, Signed accepted
- Purchase: Signed e-sign, Signed accepted

---

## 5. VALUE-ADDED MOVING - Lead Gen → Calls → Close

**Board:** Use existing board (miro.com/app/board/uXjVGMVheQw=/)
**Reference only (don't edit):** KC's board (miro.com/app/board/uXjVG7nodKQ=/)

**Frame Title:** Long Distance Moving Funnel
**Header:** Value-Added Moving - Long Distance Funnel
**Last Checked For Accuracy:** 3/27/26

### Funnel Flow:

```
[Ads] ──→ [Landing Page] ──→ [Form (3-step quote)] ──→ [Thank You Page]
                │                       │                      │
         [Call Now btn]          [Lead Created]          [Inbound Call]
          (954) 686-1474                │
                                        ▼
                                 [SMS/Email + AI SDR Outreach]
                                        │
                                        ▼
                                 ◇ Is Qualified? ◇
                                    │          │
                                   Yes        No (DQ)
                                    │
                                    ▼
                              [Raw Calls]
                                    │
                                    ▼
                           ◇ Connected? ◇
                              │        │
                             Yes      No
                              │
                              ▼
                        [Connected Calls]
                              │
                              ▼
                         ◇ Closed? ◇
                            │      │
                           Yes    No
                            │
                            ▼
                     [Sale / Cash Collected]
```

### Step Details:

| Step | Shape | Key Outcome | Arrival Count | Completion Count |
|------|-------|-------------|---------------|-----------------|
| Ads | Rectangle | Clicks | Impressions | Clicks |
| Landing Page | Rectangle | LP Visit | Clicks | LP Visits |
| Form/Lead | Rectangle | Lead | LP Visits | Leads / New Leads |
| Application | Rectangle | App Submitted | Leads | Total Apps |
| Qualification | Diamond | Qualified App | Total Apps | Qualified Apps / DQ Apps |
| Raw Calls | Rectangle | Call | Qualified Apps | Raw Calls |
| Connected | Diamond | Connected | Raw Calls | Connected Calls |
| Close | Diamond | Sale | Connected Calls | Sales (Book Date) |

### Call Sources (note on map):
- Inbound from LP phone number (954) 686-1474
- Inbound from Thank You page
- Inbound from SMS/email sequence
- AI SDR → live transfer (biz hours) or appointment (after hours)

### Rate Metrics:
- **CTR**, **Opt-in CVR**, **App %**, **QApp Rate**, **Call %**, **QApp > Call %**, **Show Rate**, **Confirmed Show Rate**, **Close Rate**, **Connected %**, **Raw Call Close %**, **Connected Call to Close %**

### Cost Metrics:
- **CPM**, **CPC**, **CPL**, **CPNL**, **Cost Per App**, **CP Qualified App**, **CPBC**, **Cost Per Show**, **CPA**, **CAC**

### Additional Metrics:
- **Revenue**, **Cash ROAS**, **AOV**, **RPC**

### URLs:
- LP: go.valueaddedmoving.com/long-distance-a
- TY: go.valueaddedmoving.com/confirmed

### LP Form:
1. Pickup Zip + Delivery Zip
2. Move Date + Move Size
3. Full Name + Email + Phone

---

## 6. THE CONTRACTOR CONSULTANTS - 3 Frames

### FRAME 1: Main Offer (Lead Form + External LP)

**Frame Title:** Main Offer Funnel
**Header:** The Contractor Consultants - Main Offer (SpamPro Advanced)

### Funnel Flow:

```
                    ┌─── [Meta Lead Form "SpamPro Advanced"] ──→ [Revenue-Based Booking Page] ───┐
                    │          (opt-in only, redirects to booking)                                │
[Ads] ─────────────┤                                                                             ├──→ [Pipeline: Lead → QA → MQL → SQL → Opp → Customer]
                    │                                                                             │
                    └─── [External LP (/meta-2)] ──→ [Form (8 Qs)] ──→ ◇ Business or Candidate? ◇
                                                                            │              │
                                                                         Business      Candidate
                                                                            │              │
                                                                            ▼              ▼
                                                                    ◇ Annual Revenue? ◇  [DQ → Fountain]
                                                                    │ (routes to booking)
                                                                    ▼
                                                            [Revenue-Based Booking Page] ──→ Pipeline
```

### Revenue-Based Booking Pages (Main Offer):
| Revenue | URL | Self-Book? |
|---------|-----|------------|
| <$500k | /ml-u500 | No |
| $500k-$1m | /ml-500-1 | No |
| $1m-$5m | /mlf1-5 | Yes |
| $5m-$10m | /mlf5-10m | Yes |
| $10m-$50m | /mlf10-50 | Yes |
| $50m+ | /mlf50m | Yes |
| Candidate | Fountain (DQ) | N/A |

### External LP Form (8 Qs):
1. Business or Candidate? → Candidate = DQ to Fountain
2. How many hires? (1 / 2-3 / 4+)
3. How soon? (1-15 days / 15-30 / 30-60 / 60+)
4. Annual revenue? (routes to booking page)
5. First & Last Name
6. Business Email (non-free = QA criteria)
7. Phone
8. Company website

---

### FRAME 2: Great Hiring Partner (Lead Form + External LP)

**Frame Title:** Great Hiring Partner Funnel
**Header:** The Contractor Consultants - Great Hiring Partner

### Funnel Flow:

```
                    ┌─── [Meta Lead Form (GHP)] ──→ [SDRs Reach Out] ──→ Pipeline
                    │        (opt-in only, NO redirect to booking)
[Ads] ─────────────┤
                    │
                    └─── [External LP (/great-hiring-partner)] ──→ [Form (8 Qs)] ──→ ◇ Business or Candidate? ◇
                                                                                          │              │
                                                                                       Business      Candidate
                                                                                          │              │
                                                                                          ▼              ▼
                                                                                  ◇ Annual Revenue? ◇  [DQ → Fountain]
                                                                                          │
                                                                                          ▼
                                                                                  [Revenue-Based Booking Page] ──→ Pipeline
```

### GHP Revenue-Based Booking Pages:
| Revenue | URL | Self-Book? |
|---------|-----|------------|
| <$500k | /ghp-m-less500k-nocal | No |
| $500k-$1m | /ghp-m-500k1m-nocal | No |
| $1m-$3m | /ghp-m-1m3m | Yes |
| $3m-$5m | /ghp-m-3m5m | Yes |
| $5m-$10m | /ghp-m-5m10m | Yes |
| $10m-$50m | /ghp-m-10m50m | Yes |
| $50m+ | /ghp-m-50m | Yes |
| Candidate | Fountain (DQ) | N/A |

**Key difference from Main Offer:** GHP Meta Lead Form goes to SDRs (no booking redirect), not directly to booking page.

---

### FRAME 3: Market Snapshot (Lead Magnet)

**Frame Title:** Market Snapshot Funnel
**Header:** The Contractor Consultants - Market Snapshot

### Funnel Flow:

```
[Ads] ──→ [External LP (/market-snapshot-meta)] ──→ [Lead Magnet Download] ──→ [Nurture/Pipeline]
```

(External LP only — no Meta lead form for this funnel)

---

### TCC Pipeline (shared across all 3 frames):

```
[Lead] ──→ ◇ Qualified App? ◇ ──→ [MQL] ──→ ◇ Disco Show? ◇ ──→ ◇ SQL? ◇ ──→ ◇ Demo Show? ◇ ──→ ◇ Closed? ◇ ──→ [Customer]
               ($1m+ rev,              (meeting         (disco        (AE score      (demo         (Deal =
               non-free email,          booked)         complete)      70+, disco     complete)     Closed Won)
               utm=facebook)                                          complete)
```

### HubSpot Lifecycle Stages:

| Stage | Trigger | Hyros Stage | Meta Event |
|-------|---------|-------------|------------|
| 1. Lead | Auto on contact creation | stage: lead | "lead" |
| 2. Qualified App | $1m+ rev, non-free email, utm=facebook | stage: qualified application | "submit application" |
| 3. MQL | Meeting booked (incl. candidates) | stage: MQL | "marketingqualifiedlead" |
| 4. SQL | AE Score 70+ AND Disco complete | stage: SQL | "salesqualifiedlead" |
| 5. Opportunity | Demo complete | stage: opportunity | "opportunity" |
| 6. Customer | Deal = Closed Won | stage: customer | "customer" |

### Rate Metrics:
- **CTR**, **Opt-in Rate**, **% Qualified Lead**, **MQL Rate**, **Disco Book Rate**, **Disco Show Rate**, **% of Disco Qualified**, **Demo Book Rate**, **Demo Show Rate**, **Live Demo Close Rate**

### Cost Metrics:
- **CPM**, **CPC**, **CPL**, **CP Qualified App**, **CPMQL**, **CP Booked Disco**, **CP Live Disco**, **CPSQL**, **CP Booked Demo**, **CPA**

### Additional: Revenue (Hyros), ROAS
