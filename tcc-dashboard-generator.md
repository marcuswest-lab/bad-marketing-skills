---
name: tcc-dashboard-generator
description: Generate a client-facing HTML dashboard from TCC ad performance CSV files. Use when the user provides new weekly CSV data and wants a dashboard report. Trigger phrases include "generate dashboard", "create report", "new weekly report", "build dashboard from CSVs".
---

# TCC Creative Ad Performance Dashboard Generator

Generates a self-contained HTML dashboard from 5 CSV files containing weekly ad performance data for TCC (The Contractor Consultants).

---

## When to Use

- User provides new weekly CSV files for TCC ad data
- User asks to generate/create/build a dashboard or report from CSV files
- User mentions TCC weekly creative performance report

## Input Requirements

The user must provide **5 CSV files** in a directory:

1. **Video Concepts** - Aggregated video creative concept performance
2. **Videos** (Video Variations) - Individual video ad variation performance
3. **Static Concepts** - Aggregated static image concept performance
4. **Statics** (Static Variations) - Individual static ad variation performance
5. **Copy** - Performance by headline and body copy combinations

### CSV Column Structure

All CSVs share this core column structure:
```
Name, Cost, New Leads, CPL, Stage: Qualified Application, CPQA,
Stage: Marketing Qualified Lead, CPMQL, Stage: Sales Qualified Lead, CPSQL,
Stage: Customer, CAC, CTR (all), CTR (link), CPM, Impressions, Clicks (all), Link Clicks
```

Video CSVs additionally have: `Hook Rate, Hold Rate, 3-sec plays, ThruPlays`

## Step-by-Step Process

### Step 1: Identify CSV Files & Date Range

- Locate all 5 CSV files in the provided directory
- Extract the date range from the filenames (e.g., "3_16 - 3_23" → "March 16–23")
- Confirm the files with the user if ambiguous

### Step 2: Parse CSV Data

**Data cleaning rules:**
- Filter out ALL rows where Cost = `$0.00` or Cost = `0`
- Filter out "Award Static" (known bad formula data)
- Filter out empty rows (trailing commas)
- Parse dollar values: remove `$`, `"`, and `,` to get float numbers
- `#DIV/0!` values → treat as `null`
- Percentage values: keep as strings (e.g., `'30.27%'`)
- Integer values with commas: remove commas, parse as integers

### Step 3: Map Data to JavaScript Objects

**VIDEO_CONCEPTS array:**
```javascript
{
  name: 'ConceptName',
  cost: 2787.77,        // parsed from "$2,787.77"
  leads: 15,            // New Leads
  cpl: 185.85,          // CPL (null if #DIV/0!)
  qa: 9,                // Stage: Qualified Application
  cpqa: 309.75,         // CPQA (null if #DIV/0!)
  mql: 9,               // Stage: Marketing Qualified Lead
  sql: 5,               // Stage: Sales Qualified Lead
  customers: 3,         // Stage: Customer
  hookRate: '30.27%',   // Hook Rate (string, '—' if #DIV/0! or 0.00%)
  holdRate: '25.61%',   // Hold Rate (string, '—' if #DIV/0!)
  ctrAll: '1.01%',      // CTR (all)
  ctrLink: '0.63%',     // CTR (link)
  cpm: 42.93,           // CPM
  impressions: 64941,   // Impressions
  plays3s: 19658,       // 3-sec plays
  thruPlays: 5035,      // ThruPlays
  link: 'https://...'   // View Ad link (see Step 4)
}
```
Sort by cost descending.

**STATIC_CONCEPTS array:**
Same as VIDEO_CONCEPTS but without hookRate, holdRate, plays3s, thruPlays.

**COPY_DATA array:**
```javascript
{
  name: 'HookName | BodyName',
  cost: 22933.72,
  leads: 104,
  cpl: 220.52,
  qa: 58,
  cpqa: 395.41,
  mql: 41,
  cpmql: 559.36,    // CPMQL stored directly from CSV
  sql: 22,
  cpsql: 1042.44,   // CPSQL stored directly from CSV
  customers: 11,
  cac: 2084.88,     // CAC stored directly from CSV
  ctrAll: '1.04%',
  ctrLink: '0.65%',
  cpm: 38.57,
  impressions: 594622
}
```
No View Ad links for Copy.

**VIDEO_VARIATIONS array:**
Same structure as VIDEO_CONCEPTS. Sort by cost descending.

**STATIC_VARIATIONS array:**
Same as STATIC_CONCEPTS. Sort by cost descending.

### Step 4: View Ad Links

**Link sources (in priority order):**
1. If a previous dashboard exists, carry over matching links by name
2. Ask the user for any missing links they want to add
3. Known persistent links:
   - VV13_WaterBomb: `https://www.facebook.com/100083296204313/posts/858165880303278/`
   - SV18_BlackText: `https://drive.google.com/file/d/1FsEalnb0X11Oj3adANNOun7ubCf-X7Rn/view?usp=drive_link`
4. Set to `null` if no link is available

**For concept-level links:** Use the top-performing variation's link (lowest CPQA with spend) to represent the concept.

### Step 5: Generate the HTML Dashboard

Use the template at `/Users/marcusawakuni/Desktop/TCC/TCC Claude Code/tcc-dashboard-3-16.html` as the reference. The output is a **single self-contained HTML file** with:

#### Structure
- Chart.js loaded via CDN
- All CSS inline in `<style>` tag
- All data hardcoded as JS `const` arrays
- All rendering logic inline in `<script>` tag

#### Layout (top to bottom)
1. **Header** - Dark gradient, title "TCC Creative Ad Performance Report", subtitle, date badge
2. **KPI Legend** - Color-coded dots explaining CPQA thresholds
3. **CPQA Bar Chart** - Horizontal bars for all concepts with QA > 0, sorted by CPQA ascending
4. **Tabbed Section** - 5 tabs: Video Concepts | Video Variations | Static Concepts | Static Variations | Copy

#### KPI Thresholds (CPQA)
- **Green** (`kpi-green`): CPQA < $500
- **Yellow** (`kpi-yellow`): $500 ≤ CPQA ≤ $600
- **Red** (`kpi-red`): CPQA > $600
- **Gray** (`kpi-na`): No qualified applications (null CPQA)

#### Table Column Order

**Video tables (Concepts & Variations):**
Name → View Ad → Cost → Leads → QA → CPQA → MQL → CPMQL → SQL → CPSQL → Customers → CAC → Hook Rate → Hold Rate → Impressions

**Static tables (Concepts & Variations):**
Name → View Ad → Cost → Leads → QA → CPQA → MQL → CPMQL → SQL → CPSQL → Customers → CAC → CTR (all) → CTR (link) → Impressions

**Copy table:**
Name → Cost → Leads → QA → CPQA → MQL → CPMQL → SQL → CPSQL → Customers → CAC → CTR (all)

#### Key JS Functions
- `fmt(n)` - Format as dollar: `$1,234.56` or `—` if null
- `fmtInt(n)` - Format as integer with commas or `—` if null
- `fmtCostPer(cost, count)` - Compute and format cost/count, `—` if count is 0 or null
- `kpiBadge(cpqa)` - Render colored badge based on KPI thresholds
- `viewBtn(link)` - Render "View Ad" button or dash if no link
- `renderTable(tableId, headers, rows)` - Generic sortable table renderer
- `sortTableBy(tableId, colIndex, type)` - Column sort handler
- `initTabs()` - Tab switching logic

### Step 6: Output & Deployment

- Save as `tcc-dashboard-{date-range}.html` in the project directory
- Open in browser to verify (use `python3 -m http.server` if needed)
- If pushing to GitHub:
  - Stage and commit the new file
  - Push to the `tcc-creative-reports` repo at `https://github.com/marcuswest-lab/tcc-creative-reports`
  - Dashboard will be available at `https://marcuswest-lab.github.io/tcc-creative-reports/`

## Common Issues

| Issue | Solution |
|-------|----------|
| `#DIV/0!` in CSV | Convert to `null`, display as `—` or `N/A` badge |
| `$0.00` cost rows | Filter out entirely |
| Missing View Ad links | Set to `null`, show dash in table |
| Dollar parsing | Strip `$`, `"`, and `,` before `parseFloat` |
| Hook/Hold Rate of `0.00%` with `#DIV/0!` hold | Show `0.00%` for hook, `—` for hold |
| Award Static | Always exclude (bad formula data) |
| Trailing empty CSV rows | Stop parsing when Name column is empty |
