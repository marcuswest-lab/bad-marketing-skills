---
name: weekly-creative-report
description: "Generate a weekly creative performance report with Top Ads, New Test Results, and Launched This Week sections. Use this skill when the user asks to create a creative report, weekly meeting notes, creative update, or test results summary for a client. Trigger when the user provides a Creative Data xlsx (weekly performance), Meta Testing Dashboard xlsx, and/or Creative Tracker xlsx files. Also trigger when the user mentions 'top ads', 'test results', 'creative report', 'meeting notes', 'creative update', 'what launched this week', or 'new test results'. This skill outputs a Google Docs-ready HTML file with hyperlinked ad names, performance metrics, and categorized test results."
---

# Weekly Creative Report Generator

## Overview

This skill generates a formatted weekly creative report for client meetings. The output is a clean HTML file that can be opened in Chrome and copy-pasted directly into Google Docs with hyperlinks preserved.

The report has three sections:
1. **Top Ads** — Best-performing creatives from the past week
2. **Main Ad Account New Test Results** — Categorized test outcomes (Winners, On the Fence, Losers, Pending/Still Testing)
3. **Launched This Week** — New creatives launched from the Creative Tracker

## Required Inputs

The user will provide some or all of:

1. **Creative Data xlsx** (weekly performance data) — Contains sheets like "All Static Variations - combine", "All Video Variations - combined", "Copy", "Static Concepts", "Video Concepts" with performance metrics per ad
2. **Meta Testing Dashboard xlsx** — Contains Q1/Q2 sheets listing all active tests with columns: Month, Test name, Test type, Channel, Active, Budget, LINK, Overview, Go Live Date, Winner / Loser, Results
3. **Creative Tracker xlsx files** (old + new format) — For extracting ad preview/folder links via embedded hyperlinks
4. **Client name** and **date range** for the report header
5. **KPI thresholds** — What CPQA (or primary metric) qualifies as a winner

## Data Sources for Each Section

**IMPORTANT: Each section uses different data with different time scopes:**

| Section | Data Source | Time Scope |
|---------|-----------|------------|
| Top Ads | Creative Data xlsx (combined sheets) | Weekly (the date range of the Creative Data file) |
| New Test Results | Old Creative Tracker performance sheets | **All-time** (to evaluate tests with enough cumulative spend) |
| Launched This Week | New Creative Tracker (launch dates) | Last 7-10 days |

## Section 1: Top Ads

### Logic
- Pull from the **combined variation sheets** in the Creative Data xlsx (e.g., "All Static Variations - combine", "All Video Variations - combined")
- **Do NOT use** the non-combined "Statics" or "Videos" sheets — those may have different/incomplete data
- Filter to creatives only (no copy) with:
  - CPQA (or primary KPI) at or below the client's winner threshold
  - Minimum 2 qualified applications (or equivalent metric) — ads with only 1 QA are not statistically meaningful
  - Cost > 0
- Sort by CPQA ascending (best first)
- Exclude test/example rows (SC1_9t5, etc.)

### Output Format
```
**Top Ads (date range):**

- [Ad Name](hyperlink)
    - $X,XXX.XX Spent
    - N leads at $XXX.XX
    - N qualified leads at $XXX.XX
    - N MQLs at $XXX.XX
    - N SQLs at $XXX.XX
    - N customers at $XXX.XX
```

### Rules
- Hyperlink the ad name to its preview/folder link
- Only include funnel stages with values > 0
- Sort by spend descending (highest spend first) or CPQA ascending — ask the user
- Use the client's terminology (e.g., "qualified leads" for TCC, "connected calls" for VAM)

## Section 2: Main Ad Account New Test Results

### Logic
1. Read the **Meta Testing Dashboard xlsx** to identify active tests
2. Focus on Creative-type tests only (skip Landing page, Lead Form, Copy tests)
3. Skip retargeting tests
4. For each test, determine its status:

**Tests launched this week** (Go Live Date within the reporting period):
- If already marked Winner/Loser/On the Fence on the dashboard → use that status
- If marked Testing or blank → evaluate using all-time performance data

**Tests still marked "Testing"** from previous weeks:
- Cross-reference against **all-time performance data** from the old Creative Tracker's performance sheets
- If the test has enough spend (≥ $500) to make a judgment:
  - CPQA ≤ winner threshold → **Winner**
  - CPQA within on-the-fence range → **On the Fence**
  - CPQA above loser threshold → **Loser**
  - Spend ≥ $500 but 0 QA → **Loser**
- If not enough spend → **Pending/Still Testing**

**Tests already resolved** (Winner/Loser from previous weeks, not launched this week):
- **Skip entirely** — these were already reported in previous weeks

### Matching Test Names to Performance Data
The Meta Testing Dashboard uses human-readable test names (e.g., "Animated/You-told-us") while the performance data uses ad naming conventions (e.g., "VV20_AnimatedAd"). Matching strategies:
- Extract keywords from the test name and search for them in performance data names
- Match by concept name (first part before " - " in new-style names)
- If a match returns data that seems too high (e.g., $500K+ spend for a single test), it matched to the entire account total — reject and mark as Pending

### Output Format
```
**Main Ad Account New Test Results**

- **Winners**
    - [Test Name](link)
        - $X,XXX.XX Spent
        - N qualified leads at $XXX.XX
        - N MQLs at $XXX.XX
        ...

- **On The Fence**
    - [Test Name](link)
        - ...

- **Losers**
    - [Test Name](link)
        - ...

- **Pending/Still Testing**
    - [Test Name](link) - launched MM/DD/YY
    - [Test Name](link) - scheduled to launch MM/DD/YY
```

### Rules
- Use the LINK column from the Meta Testing Dashboard for hyperlinks (Frame.io, Google Drive, etc.)
- For losers with 0 leads, just show "$X,XXX spent" and "0 leads"
- Include variation count for batch tests: e.g., "Animated Market Snapshot (3 variations)"
- If the Results column on the dashboard already has formatted metrics, use those as fallback
- At the bottom, add an "Updates needed on Meta Testing Dashboard" section listing which tests need their status changed

## Section 3: Launched This Week

### Logic
- Read the **New Creative Tracker** (e.g., "[Client 2.0] - Creative Tracker.xlsx")
- Check both Static Creative Tracker and Video Creative Tracker sheets
- Find all ads with a Launch Date within the reporting period (typically last 7-10 days)
- List each individual ad with its full name (not abbreviated/grouped)
- Include the Folder Link as a hyperlink

### Output Format
```
**Launched This Week (date range):**

- [Full Ad Name](folder_link) - launched M/DD
- [Full Ad Name](folder_link) - launched M/DD
```

### Rules
- Use the **full creative name** from the tracker — do NOT abbreviate or group by concept
- The name should include the angle, variation type, and any identifiers so it's clear what ad it is
- Do NOT include "Problem-Solution Body" or "Proclamation Body" suffixes — strip those
- Hyperlink to the Folder Link (Google Drive or Frame.io)
- Sort by launch date ascending

## Link Extraction

Links come from multiple sources, in priority order:

1. **Meta Testing Dashboard** — LINK column (for test results section)
2. **New Creative Tracker** — Folder Link column (col N for statics, col M for videos)
3. **Old Creative Tracker** — Performance, New Performance, File Link columns in Ad Tracker sheets
4. **Creative Data xlsx** — Ad Account sheet may have Preview link column

Always use `openpyxl` with `cell.hyperlink.target` to extract embedded hyperlinks. CSV exports lose all hyperlinks.

## HTML Output

Generate a clean HTML file styled for Google Docs copy-paste:

```html
<!DOCTYPE html>
<html><head><meta charset="UTF-8"><title>Client Creative Report</title></head>
<body style="font-family: Arial, sans-serif; font-size: 11pt; color: #000; background: #fff; max-width: 700px; margin: 20px auto; line-height: 1.5;">
<!-- Content here using <p>, <ul>, <li>, <a>, <strong> tags only -->
<!-- No CSS classes, no colors, no backgrounds — plain black text on white -->
</body></html>
```

### Rules for HTML
- Use only inline styles: `font-family: Arial; font-size: 11pt; color: #000; background: #fff`
- No dark themes, no colored badges, no fancy CSS
- Hyperlinks use default blue underline (browser default `<a>` styling)
- Use `<strong>` for section headers
- Use nested `<ul>` / `<li>` for the hierarchical structure
- Save to the client's folder with a descriptive name: `{client}_creative_report_{date_range}.html`

## Client-Specific Configuration

### TCC (The Contractor Consultants)
- Primary KPI: CPQA (Cost per Qualified Application)
- Winner threshold: CPQA ≤ $450
- On the Fence: CPQA $450-$600
- Loser: CPQA > $600 or $500+ spent with 0 QA
- Funnel: Leads → Qualified Leads → MQLs → SQLs → Customers
- Min QA for Top Ads: 2

### VAM (Value Added Moving)
- Primary KPI: ROAS and $/Connected
- Winner threshold: ROAS ≥ 1.8x
- On the Fence: ROAS 1.6-1.8x
- Loser: ROAS < 1.6x
- Funnel: Leads → New Leads → Calls In → Connected → Sales → Revenue → ROAS
- Min connected calls for Top Ads: 2

### Case Source
- Primary KPI: CAC
- Winner threshold: CAC ≤ $1,800
- Funnel: Leads → MQLs → Contacts → SQLs → Clients

### CEO Lawyer
- Primary KPI: CPS (Cost per Sale)
- Winner threshold: CPS ≤ $2,500
- Funnel: Leads → 3+ Leads → Wanted → Sales

### Nurse Coach
- Primary KPI: CPA (Cost per Sale)
- Winner threshold: CPA ≤ $1,500
- Funnel: Leads → New Leads → Typeforms → Acuity Regs → Sales

## Common Issues

1. **Test name matching failures**: The Meta Testing Dashboard uses informal names that don't match performance data names. Use keyword matching, not exact matching. If a match returns unrealistic numbers (e.g., $500K spend for one test), it matched to an account total — reject it.
2. **Duplicate entries**: The Q1 and Q2 sheets in the Meta Testing Dashboard may have overlapping entries. Deduplicate by test name.
3. **Recently launched tests**: Tests launched in the last 3-4 days won't have meaningful data yet. Always put these in Pending/Still Testing regardless of what the performance data shows.
4. **Zero-spend filtering**: Always filter out ads with $0 spend from Top Ads.
5. **Hyperlink preservation**: The HTML output must use plain `<a href="">` tags — Google Docs preserves these when copy-pasting from Chrome.
6. **Combined sheets**: Always use the "combined" or "aggregated" sheets for Top Ads, not the separate old-name/new-name sheets.
