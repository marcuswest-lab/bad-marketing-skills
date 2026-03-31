# Weekly Creative Analytics & Reporting SOP

## Overview
This SOP covers the end-to-end weekly process for pulling creative performance data, updating trackers, building dashboards, managing pipelines, and generating reports for all BAD Marketing clients.

**Cadence:** Weekly
**Team:** Shoaib (VA), Marcus, Andrew/Media Buyers, Oriana (PM)

## Timeline & Dependency Chain

Everything flows sequentially from the data pull. Nothing can start until Shoaib delivers the data on Monday.

```
MONDAY AM:            Step 1: Data Pull (Shoaib)
                          ↓
MONDAY EOD:           Step 2: Status Updates (Marcus + Andrew)
                          ↕
                      Step 4: Pipeline Updates (Oriana) [parallel with Step 2]
                          ↓
MONDAY EVE /          Step 3: Build Dashboards (Marcus)
TUESDAY AM:               ↓
                      Step 5: Creative Report (Marcus)
                          — needs dashboards + pipeline updates + everything above
```

**Critical path:** 1 → 2 → 3 → 5 (if any step is delayed, everything downstream is blocked)
**Parallel work:** Step 4 (Oriana) can start as soon as Step 1 is complete — she only needs the tracker, not the updated statuses
**Bottleneck:** Step 1 — if the data pull is late, everything shifts

## Key Links
- **Shoaib's Data Pull SOP:** https://docs.google.com/document/d/1cwjyJAV0PV0mU7ZGzYdJnED_AFV0n-QXHvq4VDeMfiQ/edit?tab=t.0#heading=h.6j0vqouls56c
- **Creative Update Doc / Pipeline Doc:** https://docs.google.com/document/d/1L5CRUueJX3Bk0KiC6RGbVhGetXhu7J4gLkV1xp6o5EU/edit?tab=t.0#heading=h.oqkbc8gfi7qj
- **Status Update Process (Loom):** https://www.loom.com/share/90137bb572c04d1b989cbd199129b1ff?from_recorder=1&focus_title=1
- **ClickUp — VAM Tracker Update:** https://app.clickup.com/t/86b91vpr9
- **ClickUp — Nurse Coach Tracker Update:** https://app.clickup.com/t/86b91qd70

---

## Step 1: Data Pull (Shoaib — Monday morning)

**Owner:** Shoaib (VA)
**When:** Monday morning (this is the starting trigger for everything else)
**SOP:** [Shoaib's Data Pull SOP](https://docs.google.com/document/d/1cwjyJAV0PV0mU7ZGzYdJnED_AFV0n-QXHvq4VDeMfiQ/edit?tab=t.0#heading=h.6j0vqouls56c)

### What gets pulled:
- [ ] **Meta Ads Manager** — Export ad-level performance data for each client (spend, leads, QA, MQL, SQL, customers, CTR, CPM, impressions)
- [ ] **Hyros** — Export attribution data (sales, revenue, ROAS, customer attribution by ad)
- [ ] **Date range:** Last 7 days + all-time (for status updates)

### Clients to pull:
- [ ] TCC (The Contractor Consultants)
- [ ] VAM (Value Added Moving)
- [ ] Case Source
- [ ] CEO Lawyer
- [ ] Nurse Coach

### Output format:
- Creative Data xlsx per client (e.g., `TCC Creative Data (3_23 - 3_29).xlsx`)
- Should contain tabs: Videos, Statics, Copy, Video Concepts, Static Concepts
- **IMPORTANT:** Must also include "All Static Variations - combine" and "All Video Variations - combined" tabs that merge old-style and new-style naming
- Raw Hyros export CSVs
- Facebook Ad Spend/Click Data CSVs (for preview links)

### Where to save:
- Project folder: `/Desktop/BAD/Claude Code BAD/[Client Name]/`

---

## Step 2: Status Updates (Marcus + Andrew — Monday EOD)

**Owner:** Marcus (TCC, CEO Lawyer, Case Source) + Andrew (VAM, Nurse Coach)
**When:** Monday EOD (after Shoaib delivers the data)
**Process video:** [Status Update Process (Loom)](https://www.loom.com/share/90137bb572c04d1b989cbd199129b1ff?from_recorder=1&focus_title=1)
**ClickUp tasks:** [VAM](https://app.clickup.com/t/86b91vpr9) | [Nurse Coach](https://app.clickup.com/t/86b91qd70)

### Process (3-step transfer):
1. **Transfer launch dates and "Testing" statuses from NEW tracker → OLD tracker**
   - Open the new tracker (e.g., `[TCC 2.0] - Creative Tracker.xlsx`)
   - Find any ads with launch dates that aren't yet in the old tracker
   - Copy those launch dates and "Testing" statuses to the old tracker
2. **Update statuses on the OLD tracker** based on performance data
   - Open the Creative Data xlsx from Step 1
   - For each ad with sufficient spend, evaluate Winner/Loser/On the Fence
   - Update the Status column in the old tracker
3. **Transfer statuses from OLD tracker → NEW tracker**
   - Copy the updated statuses back to the new tracker
   - This ensures both trackers stay in sync

### Client assignments:
| Client | Owner |
|--------|-------|
| TCC | Marcus |
| CEO Lawyer | Marcus |
| Case Source | Marcus |
| VAM | Andrew |
| Nurse Coach | Andrew |

### Evaluation criteria:
- Open the **Creative Data xlsx** from Step 1
- Open BOTH Creative Trackers for each client:
  - **Old tracker** (e.g., `[TCC] - Creative & Copy Tracking Sheet...xlsx`)
  - **New tracker** (e.g., `[TCC 2.0] - Creative Tracker.xlsx`)
- For each ad with sufficient spend (generally $500+), evaluate status:

### Status Classification Rules:
| Status | Criteria |
|--------|----------|
| **Winner** | CPQA ≤ client's green threshold (e.g., $450 for TCC) |
| **On the Fence** | CPQA between green and red thresholds (e.g., $450-$600 for TCC) |
| **Loser** | CPQA > red threshold, OR $500+ spend with 0 QAs |
| **Testing** | Not enough spend to make a judgment yet |

### Client-specific thresholds:
| Client | Winner (CPQA ≤) | On the Fence | Loser (CPQA >) | Min Spend to Judge |
|--------|-----------------|--------------|----------------|-------------------|
| TCC | $450 | $450-$600 | $600 | $500 |
| VAM | $250 (cost/connected) | $250-$350 | $350 | $500 |
| Case Source | TBD | TBD | TBD | TBD |
| CEO Lawyer | TBD | TBD | TBD | TBD |

### Where to update:
- Update status column in BOTH old and new Creative Trackers
- Update the **Meta Testing Dashboard** (xlsx) for tests that have exited testing
- Note: We're transitioning to the new tracker only — update both until migration is complete

---

## Step 3: Build Dashboards (Marcus — Monday evening / Tuesday AM)

**Owner:** Marcus
**When:** Monday evening or Tuesday AM (after status updates are done)

### Required exports:
For each client, export/download:
1. **Old Creative Tracker** (.xlsx) — has performance data sheets + ad tracker sheets with File Links
2. **New Creative Tracker** (.xlsx) — has Folder Link columns with embedded hyperlinks
3. **Creative Data xlsx** (from Step 1) — the raw weekly performance data
4. **Facebook Ad Spend/Click Data CSV** (optional) — for Facebook preview links

### Dashboard types to generate:

#### A. Weekly Dashboard (date-range specific)
- Uses data from the weekly Creative Data xlsx
- **IMPORTANT:** Pull from the "combined" tabs (e.g., "All Static Variations - combine", "All Video Variations - combined")
- Published to GitHub Pages with date slug (e.g., `tcc-creative-dashboard-mar23-29`)
- Includes: Static Ads, Static Concepts, Video Ads, Video Concepts, Copy, Copy Concepts

#### B. All-Time Dashboard (cumulative)
- Uses all-time data from old tracker performance sheets
- Supplemented with latest weekly data for new ads not yet in old tracker
- Published to GitHub Pages without date slug (e.g., `tcc-creative-dashboard`)

### Dashboard generation process (using Claude Code):
1. Place all exported files in the client's project folder
2. Use the `creative-performance-dashboard` skill
3. Specify: client name, date range, KPI thresholds
4. **Key settings per client:**
   - Extract links from BOTH tracker xlsx files using openpyxl (CSVs lose hyperlinks)
   - Merge old-style (pipe-delimited) and new-style (CID-based) naming
   - Roll up concepts from variation data (first part before ` - ` = concept name)
   - Combine duplicate concepts (e.g., `ThisIsWhatHappens` + `VV20_ThisIsWhatHappens`)
   - Exclude test rows (e.g., `SC1_9t5 | SV1_beach1`)
   - Remove Status column from weekly dashboards (statuses can be stale)
5. Push to GitHub Pages for sharing

### GitHub repos per client:
| Client | All-Time Repo | Weekly Repo Pattern |
|--------|--------------|-------------------|
| TCC | `tcc-creative-dashboard` | `tcc-creative-dashboard-mar23-29` |
| VAM | `vam-creative-dashboard` | `vam-creative-dashboard-mar23-29` |
| Case Source | `casesource-creative-dashboard` | `casesource-creative-dashboard-mar23-29` |
| CEO Lawyer | `ceolawyer-creative-dashboard` | `ceolawyer-creative-dashboard-mar23-29` |

---

## Step 4: Pipeline Updates (Oriana — Monday EOD)

**Owner:** Oriana (PM)
**When:** Monday EOD (can start as soon as Shoaib's data pull is done — runs parallel with Step 2)
**Output doc:** [Creative Update / Pipeline Doc](https://docs.google.com/document/d/1L5CRUueJX3Bk0KiC6RGbVhGetXhu7J4gLkV1xp6o5EU/edit?tab=t.0#heading=h.oqkbc8gfi7qj)

### Process:
1. Open the **New Creative Tracker** (.xlsx) for each client
2. Categorize ads into:

#### Testing Pipeline
- Has a **Folder Link** (assets ready/in-progress) but does NOT have a **Launch Date**
- Group by concept/angle name
- Include 📁 link to the Google Drive folder

#### Production Pipeline
- Has a **name on the tracker** but does NOT have a Folder Link and does NOT have a Launch Date
- The brief exists but creative assets haven't been started yet

#### Already Launched
- Has a **Launch Date** — skip entirely (already live)

### Output format:
```
**Testing pipeline:**
- Statics:
    - [Concept Name] - [Description]
        - 📁 [Folder Display Text](https://drive.google.com/...)
- Videos:
    - [Concept Name] - [Description]
        - 📁 [Folder Display Text](https://drive.google.com/...)

**In the production pipeline:**
- Statics:
    - [Concept Name] - [Description]
- Videos:
    - [Concept Name] - [Description]
```

### Automation (Oriana's Pipeline Skills):
Two Claude Code skills are available for this — Oriana can use either one:

**Skill 1: `creative-pipeline-notes`**
**Skill 2: `creative-pipeline-generator`**

Both do the same thing. In Claude Code, just say "generate pipeline notes for [client]" and provide/reference the Creative Tracker xlsx.

**What the skill does automatically:**
1. Reads the Video Creative Tracker and Static Creative Tracker sheets
2. Categorizes ads based on Folder Link and Launch Date presence:
   - **Testing pipeline:** Has Folder Link but NO Launch Date
   - **Production pipeline:** Has a name but NO Folder Link and NO Launch Date
   - **Already launched:** Has Launch Date → skipped
3. Groups by concept/angle name (deduplicates variations)
4. Extracts Google Drive hyperlinks using openpyxl (preserves links that CSVs lose)
5. Outputs formatted notes with 📁 links ready to copy-paste into the meeting doc

**Input:** New Creative Tracker xlsx per client (e.g., `[TCC 2.0] - Creative Tracker.xlsx`)
**Output:** Formatted pipeline notes matching the template above

---

## Step 5: Creative Reports (Marcus — Monday evening / Tuesday morning)

**Owner:** Marcus
**When:** Monday evening or Tuesday morning (after dashboards + pipeline updates are ready)

### Report components:

#### A. Top Ads Report
- Pull top-performing ads with CPQA ≤ client threshold
- **Filter:** Creatives only (no copy), minimum 2+ qualified applications
- **Data source:** Weekly Creative Data xlsx, combined tabs
- **Links:** Hyperlink ad names using links from Creative Trackers
- **Format:**
```
Top Ads (date range):

- [Ad Name](link)
    - $X,XXX.XX Spent
    - N leads at $XXX.XX
    - N qualified leads at $XXX.XX
    - N MQLs at $XXX.XX
    - N SQLs at $XXX.XX
    - N customers at $XXX.XX
```
- Use the `top-ads-report` skill

#### B. New Test Results (from Meta Testing Dashboard)
- **Scope:** Only report on:
  1. Tests launched THIS WEEK (last 7 days)
  2. Tests still marked "Testing" that now have enough data to categorize
- **Skip:** Previously reported winners/losers/on-the-fence (unless launched this week)
- **Skip:** Retargeting tests
- **Data source:** All-time performance data (not just this week)
- **Format:**
```
Main Ad Account New Test Results

- Winners
    - [Test Name](link)
        - $X,XXX.XX Spent
        - N leads at $XXX.XX
        - N qualified applications at $XXX.XX
        ...

- On The Fence
    - ...

- Losers
    - ...

- Pending/Still Testing
    - [Test Name](link) - launched MM/DD/YY

⚡ Updates needed on Meta Testing Dashboard:
- Test Name → Change from "Testing" to "Winner"
```

#### C. Creative Pipeline Update (from Oriana's output in Step 4)

#### D. Dashboard Links
- Include links to the published GitHub Pages dashboards

### Where to send:
- Paste into the weekly meeting notes Google Doc
- Post dashboard links in the client's Slack channel
- Output as HTML file for easy copy-paste with hyperlinks preserved

---

## Automation Opportunities

### Currently Automated (via Claude Code skills):
- ✅ Dashboard generation from xlsx/CSV data
- ✅ Top Ads report generation
- ✅ Pipeline notes generation
- ✅ GitHub Pages publishing
- ✅ Link extraction from Creative Trackers

### Partially Automated:
- 🟡 Test results categorization (needs manual matching between test names and ad names)
- 🟡 Concept roll-ups (automated but sometimes needs manual concept name deduplication)

### Not Yet Automated (highest leverage opportunities):
- ❌ **Data pull from BigQuery → Creative Tracker** (eliminates Step 1 manual work)
- ❌ **Status auto-classification** based on KPI thresholds (reduces Step 2)
- ❌ **Meta Testing Dashboard auto-update** (could auto-match test names to performance data)
- ❌ **Slack notifications** with dashboard links and top ads summary

### Future: BigQuery Pipeline
Once MarTech provides BigQuery access:
1. BigQuery → Google Sheets (native connector, auto-refresh)
2. Google Sheets = Creative Tracker (always up-to-date)
3. Claude Code reads the sheet → generates dashboards automatically
4. Scheduled task publishes to GitHub Pages weekly

---

## Quick Reference: File Locations

```
/Desktop/BAD/Claude Code BAD/
├── TCC/
│   ├── [TCC] - Creative & Copy Tracking Sheet...xlsx  (old tracker)
│   ├── [TCC 2.0] - Creative Tracker.xlsx              (new tracker)
│   ├── TCC Creative Data (3_23 - 3_29).xlsx           (weekly data)
│   ├── Meta Testing Dashboard.xlsx                     (test tracking)
│   └── tcc_creative_dashboard_mar23_29.html            (weekly dashboard)
├── VAM/
│   ├── VAM Claude Code/
│   │   ├── [VAM] - Creative & Copy Tracking Sheet...xlsx
│   │   └── [Value Added Moving] - Creative Tracker.xlsx
│   └── ...
├── Case Source/
├── CEO Lawyer/
├── Nurse Coach/
└── creative dashboard skill.md                         (master skill doc)
```
