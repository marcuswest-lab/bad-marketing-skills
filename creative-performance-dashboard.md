---
name: creative-performance-dashboard
description: "Build a weekly Creative Performance Dashboard from Excel tracker files and Facebook ad spend data. Use this skill whenever the user asks to create a creative performance report, build a creative dashboard, generate a weekly ad performance report, or uploads Excel/CSV files with columns like CPQA, ROAS, Cost per Connected, Stage: Qualified Application, Hook Rate, Hold Rate, or mentions video variations, static variations, video concepts, static concepts, copy concepts, or copy performance tabs. Also trigger when the user mentions ad creative analysis, cost per qualified application, cost per connected call, Facebook ad preview links, or wants to see top-performing ads. This skill covers the full pipeline: parsing Excel files with openpyxl (always preferred over CSVs), extracting hyperlinks from tracker tabs, concept roll-up aggregation, generating an interactive HTML dashboard with sortable tables and KPI color coding, and producing a top-ads text summary report."
---

# Creative Performance Dashboard

## Overview

This skill generates a weekly interactive HTML dashboard for a client's creative ad performance data. It parses Excel files (always preferred over CSVs) from the client's reporting system, extracts ad preview/file links from tracker tabs (including embedded hyperlinks), and produces a dark-themed sortable dashboard with up to six tabs: Static Ads, Static Concepts, Video Ads, Video Concepts, Copy, and Copy Concepts.

The dashboard is client-agnostic — adapt the title, date range, KPI thresholds, and naming conventions to each client. Ask the user for client-specific details if not provided.

**CRITICAL: Always prefer Excel (.xlsx) over CSV.** Excel files preserve embedded hyperlinks (File Links, Folder Links, Preview Links) that CSVs lose entirely. A single Excel tracker file often contains both the performance data sheets AND the ad tracker sheets with links — always check all sheets.

## Required Inputs

The user will provide some or all of:

1. **Creative Data** — **Always prefer Excel (.xlsx) files over CSVs:**
   - **Excel tracker files** (`.xlsx`) are the single source of truth. A single Excel file often contains BOTH performance data sheets AND tracker sheets with embedded links. Always open every xlsx file with `openpyxl` and inspect ALL sheets before parsing.
   - **Two common Excel file types:**
     - **Old-format tracker** (`*Creative & Copy Tracking Sheet*`): Contains Ad Tracker tabs (with `Creative Name`, `File Link`, `New Name` columns) AND Performance tabs (with `Ad Name`, `Cost`, `Leads`, etc.)
     - **New-format tracker** (`*Creative Tracker*` or `*2.0*`): Contains tracker tabs (with `Static Creative Name`, `Folder Link`, CID-based names) — may not have performance tabs
   - **Performance Report CSVs** (e.g., `Report 15-01-2026 - 18-03-2026.csv`) — use as a supplementary data source for newer ad-level performance data, especially when the Excel performance tabs don't cover the full date range
   - **Facebook Ad Spend/Click Report CSVs** — useful for two things: (1) preview links via `Preview link` column, and (2) click/impression data (CTR, CPM) via `Impressions`, `Clicks (all)`, `Link clicks` columns
   - Performance data tabs: Static Ad Performance, Video Ad Performance, Copy Performance
   - Concept data: Usually generated via roll-up from variation-level data (not separate files)

2. **Tracker files** (for ad preview/file links — **always look for these**):
   - **ALWAYS extract links from Excel files using openpyxl** — CSV exports lose all hyperlinks
   - **Old-format tracker xlsx** — Look for `*Creative & Copy Tracking Sheet*`. Has sheets like "Static Ad Tracker", "Video Ad Tracker", "Copy Tracker" with columns: `Creative Name`, `File Link` (Google Drive/Dropbox links), `New Name` (alternate name mapping). Link columns to check: `File Link`, `File Folder`, `Performance`, `New Performance`.
   - **New-format tracker xlsx** — Look for `*Creative Tracker*` or `*2.0*`. Has sheets like "Static Creative Tracker", "Video Creative Tracker", "Copy Tracker" with columns: `Static Creative Name` / `Video Creative Name` / `Copy Name`, `Folder Link`. Uses CID-based naming.
   - **Facebook Ad Report CSVs** — Look for `Untitled-report-*.csv` or similar exports with a `Preview link` column. These contain Facebook ad preview URLs. Match to dashboard ads by ad name (keep highest-spend link per ad).
   - Some clients have **two or three link sources** (old tracker + new tracker + Facebook report) — extract from ALL and merge, prioritizing: (1) Facebook preview links, (2) new tracker links, (3) old tracker links

3. **Date range** for the report header

4. **Client name** for the report title (ask if not provided)

5. **KPI thresholds** (ask if not provided — these vary significantly by client):
   - Primary KPI (e.g., ROAS, CPQA, Cost per Connected) with green/yellow/red cutoffs
   - Secondary KPIs with thresholds
   - Hook Rate / Hold Rate cutoffs (video tabs only)

## Configurable KPI Thresholds

Ask the user for their KPI targets. There are no universal defaults — thresholds depend on the client's business model (e.g., lead gen vs e-commerce, high-ticket vs low-ticket). Common KPI types:

**Cost-based KPIs** (lower is better — green ≤ threshold):
- CPQA (Cost per Qualified Application)
- Cost per Connected Call
- CAC (Cost per Sale / Customer Acquisition Cost)
- CPL (Cost per Lead)

**Rate/Ratio KPIs** (higher is better — green ≥ threshold):
- ROAS (Return on Ad Spend)
- Hook Rate / Hold Rate (video only)
- Close Rate

**Color system:**
| Status | Color |
|--------|-------|
| Within KPI | Green (`#34D399`) |
| On the Fence | Yellow (`#FBBF24`) |
| Above/Below KPI | Red (`#F87171`) |
| No Data | Gray muted (`#475569`) |

**Badge class names:** `wk` (within KPI/green), `of` (on the fence/yellow), `ak` (above KPI/red), `nd` (no data/gray)

## CSV / Excel Column Structure

Column names vary by client. **Always inspect the actual headers first** before writing parsing code. Common patterns:

### Core columns (most clients):
- `Ad Name` / `Name` — Creative name
- `Cost` — Dollar amount (may have `$` and commas)
- `Leads`, `Cost per Lead` / `CPL`
- `Impressions`, `CTR (all)`, `CTR (link)`, `CPM`
- `Status` — Winner/Loser/Testing/On the Fence/Paused

### Call/sales funnel columns (lead-gen clients):
- `New Leads`, `Cost per New Lead`
- `Stage: Incoming Call`, `cost per incoming call`
- `api-outbound-call-outbound / Calls` (outbound calls), `Cost per Outbound Call`
- `Stage: Inbound Connected Call`, `cost per inbound connected`
- `Outbound connected calls`, `cost per outbound connected`
- `Connected (Total)`, `cost per connected (total)`
- `connected close rate (total)`
- `Sales`, `Cost per Sale`, `Revenue`, `ROAS`, `Profit`

### PI / legal lead-gen funnel columns:
- `New Leads`, `Cost per New Lead`
- `Stage: 3 Severity` / `3+ Leads`, `Cost per 3 Severity` / `Cost per 3+ Lead`
- `Stage: Wanted Lead (SQL)` / `Wanted Leads`, `Cost per Wanted Lead`
- `Stage: Customer` / `Sales`, `Cost per Signed Case` / `Cost per Sale`

### E-commerce / application funnel columns:
- `Stage: Qualified Application`, `CPQA`
- `Stage: Marketing Qualified Lead`, `CPMQL`
- `Stage: Sales Qualified Lead`, `CPSQL`
- `Stage: Customer`, `CAC`

### Video-specific columns:
- `Hook Rate`, `Hold Rate`
- `3-sec plays` / `3 Sec Views`, `ThruPlays`
- `Video Plays at 25%/50%/75%/100%`

**Important**: Invalid values appear as `#DIV/0!` or `#REF!` — these should be treated as null/`—`.

## Creative Naming Conventions

Clients use different naming systems. **Inspect the actual data** to identify the pattern. Two common formats:

### Old-style: Pipe-delimited with prefix codes
```
SC7_CheckThis | SV8_Gmail          (static: concept | visual variation)
VHK1_MovingNoStress | VB1_NoBody | VV1_TruckAcrossUS   (video: hook | body | visual)
CHK1_SaveUpTo50 | CB1_Savings      (copy: hook | body)
```
Common prefixes: VHK (Video Hook), VB (Video Body), VV (Video Visual/Concept), SC (Static Concept), SV (Static Variation), CHK (Copy Hook), CB (Copy Body)

### New-style: Dash-delimited with descriptive names
```
Stranger Danger Validation - Story Lead 1 - CIDR8KIRXC
Cost Shock Revelation - Problem-Solution Lead 2 - CIDAMRGS3U
VAM Crew Members - Organic Looking 1 - CIDQG45LVZ
```
Format: `[Angle Name] - [Lead Type] [Variation Type] [N] - [CID]`

Both formats may coexist in the same dataset.

## Data Processing Steps

### Step 1: Discover and Parse Data Sources

**ALWAYS start by scanning the client folder for ALL xlsx and csv files.** Open every xlsx file with openpyxl and list all sheets — a single xlsx often contains both tracker tabs and performance tabs.

**Priority order for performance data:**
1. **Excel performance tabs** (e.g., "Static Ad Performance", "Video Ad Performance" sheets inside tracker xlsx) — preferred because they're in the same file as the link data
2. **Report CSVs** (e.g., `Report 15-01-2026 - 18-03-2026.csv`) — supplementary data for newer date ranges
3. **Standalone performance CSVs** — fallback if no Excel is available

**Priority order for ad links:**
1. **Excel tracker tabs** (File Link, Folder Link columns) — use openpyxl, NEVER csv.reader
2. **Facebook Ad Report CSVs** (Preview link column) — Facebook preview URLs
3. **Click Data CSVs** — for CTR/CPM/Impressions data (not links)

```python
import pandas as pd
import openpyxl
import re

# Step 1a: Scan all xlsx files and list their sheets
def scan_excel_files(folder_path):
    """Discover all Excel files and their sheets."""
    import glob
    xlsx_files = glob.glob(f"{folder_path}/*.xlsx")
    for f in xlsx_files:
        wb = openpyxl.load_workbook(f, data_only=True, read_only=True)
        print(f"  {f}: {wb.sheetnames}")
        wb.close()

# Step 1b: Parse performance data from Excel sheets
def parse_performance_from_excel(file_path, sheet_name):
    """Read performance data directly from Excel sheet using pandas."""
    df = pd.read_excel(file_path, sheet_name=sheet_name)
    return df

def parse_dollar(v):
    if pd.isna(v): return None
    if isinstance(v, (int, float)): return float(v)
    s = str(v).replace('$','').replace(',','')
    try: return float(s)
    except: return None

def clean_pct(v):
    if pd.isna(v) or str(v).strip() in ('', '#DIV/0!', '#REF!', 'nan'): return None
    if isinstance(v, (int, float)): return round(v * 100, 2) if abs(v) < 1 else round(v, 2)
    s = str(v).replace('%','').strip()
    try: return round(float(s), 2)
    except: return None

def clean_num(v):
    if pd.isna(v) or str(v).strip() in ('', '#DIV/0!', '#REF!', 'nan', '-'): return None
    s = str(v).replace(',','')
    try: return float(s)
    except: return None
```

- Filter to rows with Cost > 0 only
- Parse all dollar values by stripping `$` and `,`
- Convert `#DIV/0!`, `#REF!`, and `-` to null/`—`
- **ALWAYS exclude known example/test rows** — these are dummy data used as templates in the tracker and must never appear in dashboards. Known test row patterns: `SC1_9t5`, `VHK1_9t5`, `SV1_beach1`, any row with `9t5` in the name. Filter these out automatically without asking the user.
- **Include columns that have meaningful data** — inspect all rows to determine which columns actually contain values. Omit columns that are entirely empty/zero across all rows (e.g., if no ads have Revenue data, don't show Revenue). The goal is a clean, usable table, not a wall of empty dashes.
- **Combine old + new data**: When a client has both old tracker performance tabs (all-time) and newer Report CSVs (recent date range), combine them. Aggregate by creative name — sum cost/counts, recalculate cost-per metrics. If same creative exists in both sources, merge the data.

### Step 2: Extract Ad Preview / File Links from ALL Excel Tracker Tabs

**CRITICAL: Links MUST be extracted from Excel (.xlsx) files using openpyxl. CSV exports lose all embedded hyperlinks.** Always check every xlsx file in the client folder for tracker sheets.

Links are stored as embedded hyperlinks in Excel cells, NOT as plain text URLs. The cell may display "See Here" or other text while the actual URL is in `cell.hyperlink.target`.

**Link extraction strategy — check ALL of these sources in order:**

1. **Old-format tracker xlsx** (`*Creative & Copy Tracking Sheet*`):
   - Sheets: "Static Ad Tracker", "Video Ad Tracker", "Copy Tracker"
   - Name column: `Creative Name`
   - Link columns (check ALL): `File Link`, `File Folder`, `Performance`, `New Performance`
   - Also has: `New Name` column — map both old and new names to the same link
   - Header row is typically row 1, data starts at row 2

2. **New-format tracker xlsx** (`*Creative Tracker*` or `*2.0*`):
   - Sheets: "Static Creative Tracker", "Video Creative Tracker", "Copy Tracker"
   - Name columns: `Static Creative Name`, `Video Creative Name`, `Copy Name`
   - Link column: `Folder Link`
   - May have a sub-header row (row 2) — data typically starts at row 3

3. **Facebook Ad Report CSVs** (`Untitled-report-*.csv` or similar):
   - Columns: `Ad name`, `Preview link`, `Amount spent (USD)`
   - Match by ad name — keep highest-spend link per ad
   - These provide Facebook preview URLs (most useful for viewing the actual ad)

```python
import openpyxl

def extract_links_from_tracker(file_path, sheet_name, name_header='Creative Name', data_start_row=2):
    """Extract links from tracker tabs using openpyxl (reads embedded hyperlinks).

    Args:
        file_path: Path to xlsx file
        sheet_name: Sheet name to read
        name_header: Column header for creative name
        data_start_row: Row where data starts (2 for old format, 3 for new format with sub-headers)
    """
    wb = openpyxl.load_workbook(file_path, data_only=True)
    ws = wb[sheet_name]
    headers = {}
    for cell in ws[1]:
        if cell.value:
            headers[str(cell.value).strip()] = cell.column

    name_col = headers.get(name_header)
    new_name_col = headers.get('New Name')  # Old-format trackers have this

    # Try ALL possible link column names
    link_cols = []
    for col_name in ['File Link', 'Folder Link', 'File Folder', 'Performance', 'New Performance', 'Preview Link']:
        if col_name in headers:
            link_cols.append(headers[col_name])

    link_map = {}
    for row in ws.iter_rows(min_row=data_start_row, max_row=ws.max_row):
        name = str(row[name_col-1].value or '') if name_col else ''
        if not name or name in ('None', 'nan', ''): continue

        # Check ALL link columns, prefer File Link > Folder Link > Performance
        best_link = None
        for lc in link_cols:
            cell = row[lc-1]
            val = str(cell.value or '')
            if 'http' in val.lower():
                best_link = val
                break
            elif cell.hyperlink and cell.hyperlink.target and 'http' in str(cell.hyperlink.target).lower():
                best_link = cell.hyperlink.target
                break

        if best_link:
            link_map[name.strip()] = best_link
            # Also map the New Name if available
            if new_name_col:
                new_name = str(row[new_name_col-1].value or '')
                if new_name and new_name not in ('None', 'nan', ''):
                    link_map[new_name.strip()] = best_link

    wb.close()
    return link_map

def extract_fb_preview_links(csv_path):
    """Extract Facebook preview links from ad report CSV."""
    import csv
    links = {}
    with open(csv_path) as f:
        reader = csv.DictReader(f)
        for row in reader:
            # Handle BOM in header
            name = row.get('\ufeffAd name') or row.get('Ad name') or row.get('\ufeff"Ad name"', '')
            preview = row.get('Preview link', '')
            spend = 0
            try: spend = float(row.get('Amount spent (USD)', '0').replace(',',''))
            except: pass
            if preview and 'http' in preview:
                name = name.strip()
                if name not in links or spend > links[name][1]:
                    links[name] = (preview.strip(), spend)
    return {k: v[0] for k, v in links.items()}
```

**Key points:**
- **Always use openpyxl** — NEVER rely on CSV exports for link data
- Cells may show "See Here" or other display text while containing an `=HYPERLINK()` formula — use `cell.hyperlink.target` to get the actual URL
- Check ALL link columns in order: `File Link`, `Folder Link`, `File Folder`, `Performance`, `New Performance`
- **Always check for `New Name` column** — old-format trackers have both old and new names for the same creative. Map BOTH names to the link.
- Some clients have **two or three tracker files** — extract from ALL and merge
- **Facebook preview links** are the best for viewing the actual ad — prioritize these when available
- Link types vary: Google Drive folder links, Google Drive file links, Dropbox links, Facebook preview links — all work as "View" links

### Step 3: Match Links from Facebook Ad Spend Report

If a Facebook ad spend CSV is provided (instead of or in addition to tracker tabs), match preview links:

1. Split ad name by `|`, identify creative identifier parts vs copy/date parts
2. Join creative parts to form a variation key
3. For each variation, keep the preview link with highest spend
4. For concepts, aggregate best link per concept identifier
5. For copy, match by copy hook identifier

**CRITICAL: URL encoding in HTML**
Facebook preview links contain `&h=` which gets eaten by the HTML parser. Use `\\u0026` instead of `&` in JavaScript string literals within the HTML file:
```javascript
// WRONG - &h= gets interpreted as HTML entity
link:"https://www.facebook.com/?feed_demo_ad=123&h=AQIxyz"

// CORRECT - \u0026 is interpreted by JS as &
link:"https://www.facebook.com/?feed_demo_ad=123\\u0026h=AQIxyz"
```

### Step 4: Concept Roll-Up (when concept-level CSVs are NOT provided)

If the user only provides variation-level data (no separate concept CSVs), **generate concept tabs by rolling up variations**. This is the more common case.

#### Concept name extraction

Extract the core concept/angle name from each ad name, stripping variation-level detail:

```python
def normalize_old_concept(raw):
    """Strip version/holiday/CTA suffixes to get core concept name.
    e.g. CheckThisHolidayV3 -> CheckThis, LongDistance1597CTA1 -> LongDistance1597,
    CityPricesV4 -> CityPrices, CoastToCoast-V2 -> CoastToCoast"""
    raw = re.sub(r'CTA\d+$', '', raw)
    raw = re.sub(r'Holiday(V\d+)?$', '', raw)
    raw = re.sub(r'NY(V\d+)?$', '', raw)
    raw = re.sub(r'-?V\d+$', '', raw)
    return raw.strip('-').strip()

def extract_static_concept(name):
    if '|' in name:
        sc_part = name.split('|')[0].strip()
        m = re.match(r'SC\d+_(.+)', sc_part)
        if m: return normalize_old_concept(m.group(1))
        return sc_part
    elif ' - ' in name:
        return name.split(' - ')[0].strip()
    return name

def extract_video_concept(name):
    if '|' in name:
        parts = [p.strip() for p in name.split('|')]
        vv = [p for p in parts if p.startswith('VV')]
        if vv:
            m = re.match(r'VV\d+_(.+)', vv[0])
            raw = m.group(1) if m else vv[0]
            return normalize_old_concept(raw)
        return parts[0]
    elif ' - ' in name:
        return name.split(' - ')[0].strip()
    return name

def extract_copy_concept(name):
    if '|' in name:
        chk_part = name.split('|')[0].strip()
        m = re.match(r'CHK\d+_(.+)', chk_part)
        if m: return normalize_old_concept(m.group(1))
        return chk_part
    elif ' - ' in name:
        return name.split(' - ')[0].strip()
    return name
```

**Important normalization rules:**
- Strip trailing version numbers: V1, V2, V3, -V2
- Strip Holiday variants: Holiday, HolidayV1, HolidayV2
- Strip NY (New Year) variants: NY, NYV1, NYV2
- Strip CTA suffixes: CTA1, CTA2
- For new-style names (`Angle Name - Lead Type N - CID`), the concept is everything before the first ` - `
- Both old-style and new-style names should coexist and roll up correctly

#### Roll-up aggregation

```python
from collections import defaultdict

def rollup(rows, concept_fn, is_video=False):
    groups = defaultdict(list)
    for r in rows:
        concept = concept_fn(r['name'])
        groups[concept].append(r)

    rolled = []
    for concept, variations in groups.items():
        # Sum all additive metrics
        cost = sum(v['cost'] or 0 for v in variations)
        leads = sum(v['leads'] or 0 for v in variations)
        new_leads = sum(v.get('newLeads') or 0 for v in variations)
        incoming = sum(v['incoming'] or 0 for v in variations)
        outbound = sum(v.get('outbound') or 0 for v in variations)
        inbound_connected = sum(v.get('inboundConnected') or 0 for v in variations)
        outbound_connected = sum(v.get('outboundConnected') or 0 for v in variations)
        connected = sum(v['connected'] or 0 for v in variations)
        sales = sum(v['sales'] or 0 for v in variations)
        revenue = sum(v['revenue'] or 0 for v in variations)
        profit = sum(v['profit'] or 0 for v in variations)
        impressions = sum(v['impressions'] or 0 for v in variations)

        row = {
            'name': concept,
            'variations': len(variations),
            'cost': cost if cost > 0 else None,
            'leads': leads if leads > 0 else None,
            'cpl': (cost / leads) if leads > 0 else None,
            'newLeads': new_leads if new_leads > 0 else None,
            'cpnl': (cost / new_leads) if new_leads > 0 else None,
            # ... recalculate ALL cost-per metrics from summed numerators
            'connected': connected if connected > 0 else None,
            'costConnected': (cost / connected) if connected > 0 else None,
            'sales': sales if sales > 0 else None,
            'cps': (cost / sales) if sales > 0 else None,
            'revenue': revenue if revenue > 0 else None,
            'roas': (revenue / cost) if cost > 0 and revenue > 0 else None,
            'profit': profit,
            'ctrAll': None,  # Cannot sum percentages — leave null for concepts
            'ctrLink': None,
            'cpm': (cost / impressions * 1000) if impressions > 0 else None,
            'impressions': impressions if impressions > 0 else None,
        }

        # Video metrics: weighted average by impressions
        if is_video:
            hook_total = sum((v.get('hookRate') or 0) * (v.get('impressions') or 0) for v in variations)
            hold_total = sum((v.get('holdRate') or 0) * (v.get('impressions') or 0) for v in variations)
            total_impr = sum(v.get('impressions') or 0 for v in variations)
            row['hookRate'] = round(hook_total / total_impr, 1) if total_impr > 0 and hook_total > 0 else None
            row['holdRate'] = round(hold_total / total_impr, 1) if total_impr > 0 and hold_total > 0 else None

        # Pick link from top-spending variation that has a link
        linked = [v for v in variations if v.get('link')]
        if linked:
            top_var = max(linked, key=lambda v: v.get('cost') or 0)
            row['link'] = top_var['link']
        else:
            row['link'] = None

        rolled.append(row)

    rolled.sort(key=lambda x: -(x['roas'] if x['roas'] is not None else -999))
    return rolled
```

**Roll-up rules:**
- **Additive metrics** (cost, leads, sales, revenue, impressions, etc.): SUM across variations
- **Cost-per metrics** (CPL, CPQA, CAC, etc.): RECALCULATE from summed cost / summed count — never average the cost-per values
- **Rate metrics** (Hook Rate, Hold Rate): WEIGHTED AVERAGE by impressions
- **Percentage metrics** (CTR, Close Rate): Cannot be summed — leave null for concepts, or recalculate from clicks/impressions if available
- **Link**: Use the link from the highest-spending variation that has one
- Show variation count as "(N ads)" next to concept name

### Step 5: Data Integrity Check

Concept-level total spend may exceed the sum of variation-level rows because some variation rows may be missing from the export. Flag this to the user if the gap is significant — concepts are typically the source of truth for totals.

## Dashboard Specification

### Design System

- **Theme**: Dark background (`#0C0F14`), surface cards (`#151922`), table borders (`#1E293B`)
- **Font**: DM Sans (Google Fonts) for UI text, JetBrains Mono (Google Fonts) for numbers/data cells
- **Max width**: 1700px centered container
- **Body padding**: `24px 24px 48px`
- **Color coding**: Green/Yellow/Red badge system for KPIs
- **No summary KPI bar**: Do not include an aggregate KPI summary section at the top. The dashboard is table-only with a legend, search/filter controls, and tabs.

### Page Layout (top to bottom)

The layout must be consistent across all client dashboards:

1. **Header** — centered (`text-align: center`)
   - Title: `h1`, 28px, font-weight 700, color `#F8FAFC`
   - Subtitle: 14px, color `#94A3B8`, format: `[Client Name] — All-Time Performance Data | Generated [Date]`
2. **Legend** — centered below header, flex with `justify-content: center`, gap 24px, font-size 12px
   - Colored dots (10px circles) with threshold descriptions
   - Updates dynamically when switching to video tabs (adds Hook/Hold thresholds)
   - Legend uses pipe `|` separators within each tier, e.g.: `CAC ≤ $1,800 | CPL ≤ $100`
3. **Controls row** — left-aligned flex, gap 10px
   - Search input (280px wide, dark background `#151922`, border `#334155`, rounded 8px)
   - Filter buttons: "Winners Only", "Min $X Spend" (threshold is client-specific)
4. **Tab bar** — contained in a single rounded bar (`background: #151922`, `border-radius: 10px`, `border: 1px solid #1E293B`)
   - Tabs are side-by-side with `border-right: 1px solid #1E293B` between them
   - Active tab: `background: #3B82F6`, white text
   - Inactive tab: `color: #64748B`, hover to `#CBD5E1`
   - **Each tab shows its row count** in parentheses: e.g., `Static Ads (72)` — use a `<span class="cnt">` with reduced opacity
   - Tab bar should be `width: fit-content` (not full width)
5. **Data table** — wrapped in `overflow-x: auto` container with `border-radius: 10px`, `border: 1px solid #1E293B`
6. **Row count** — right-aligned below table, `color: #475569`, font-size 12px, format: `N of M creatives`

### Status Badges

Ads may have a `Status` field. Render as colored badges:
- **Winner**: Green badge (`rgba(52,211,153,.15)` bg, `#34D399` text)
- **Loser**: Red badge (`rgba(248,113,113,.15)` bg, `#F87171` text)
- **Testing**: Blue badge (`rgba(96,165,250,.15)` bg, `#60A5FA` text)
- **On the Fence**: Yellow badge (`rgba(251,191,36,.15)` bg, `#FBBF24` text)
- **Past Winner / Paused / Killed**: Gray badge (`rgba(71,85,105,.15)` bg, `#94A3B8` text)

Concept tabs show **both** a "Status" column (Winner/Loser/Testing/On the Fence badges) **and** a "# Ads" count column. If the concept-level data source has a Status column, use it directly. If generating concepts via roll-up and no status is provided, determine status from KPI thresholds (e.g., CPQA ≤ green threshold = Winner, CPQA > red threshold = Loser, otherwise On the Fence/Testing based on spend level).

### KPI Badge Functions

```javascript
// Generic KPI badge — works for both "lower is better" and "higher is better"
function ccBadge(v, metric) {
  if (v == null || v === 0) return 'nd';
  const k = KPI[metric];
  if (!k) return 'nd';
  // Higher-is-better metrics (ROAS, hook rate, hold rate, close rate)
  if (metric === 'roas' || metric === 'hookRate' || metric === 'holdRate' || metric === 'closeRate') {
    return v >= k.green ? 'wk' : v >= k.yellow ? 'of' : 'ak';
  }
  // Lower-is-better metrics (cost per X)
  return v <= k.green ? 'wk' : v <= k.yellow ? 'of' : 'ak';
}
```

**Badge CSS classes:**
- `.wk` (within KPI): `background: rgba(52,211,153,.15); color: #34D399`
- `.of` (on the fence): `background: rgba(251,191,36,.15); color: #FBBF24`
- `.ak` (above/below KPI): `background: rgba(248,113,113,.15); color: #F87171`
- `.nd` (no data): `color: #475569` (no background, just dash `—`)

### Six Tabs (maximum)

| Tab | Data Source | Has Video Metrics | Concept Tab |
|-----|-----------|-------------------|-------------|
| Static Ads | Static performance data | No | No |
| Static Concepts | Roll-up or separate CSV | No | Yes |
| Video Ads | Video performance data | Yes (Hook/Hold) | No |
| Video Concepts | Roll-up or separate CSV | Yes (Hook/Hold) | Yes |
| Copy | Copy performance data | No | No |
| Copy Concepts | Roll-up or separate CSV | No | Yes |

Only include tabs for which data is provided. All tabs should have link columns if preview links are available.

### Table Columns

Include only columns that have meaningful data for the client. **Omit columns that are entirely empty or unused** across all rows (e.g., if no rows have Revenue/ROAS data, don't include those columns). A typical column set for different client types:

**Lead-gen with call funnel (e.g., VAM):**
`#, Name, Ad, Status/# Ads, Spend, Leads, CPL, New Leads, CPNL, Calls In, $/Call In, Outbound, $/Outbound, Inb Connected, $/Inb Conn, Outb Connected, $/Outb Conn, Connected, $/Connected, Close %, Sales, CAC, Revenue, ROAS, Profit, CTR (all), CTR (link), CPM, Impr`

**Lead-gen with application funnel (e.g., Case Source):**
`#, Name, Ad, Status/# Ads, Spend, Leads, CPL, MQL, $/MQL, Contacts, $/Contact, SQLs, $/SQL, Clients, CAC, CTR (all), CTR (link), CPM, Impr`

**PI/Legal lead-gen funnel (e.g., CEO Lawyer):**
`#, Name, Ad, Status/# Ads, Spend, Leads, CPL, 3+ Leads, $/3+ Lead, Wanted, $/Wanted, Sales, CPS, CTR (all), CTR (link), CPM, Impr`

**Video tabs** additionally include: `Hook %, Hold %` (inserted after CAC, before CTR columns)

Adapt columns to match the client's actual data. End the table with engagement metrics (CTR, CPM, Impressions) as the rightmost columns.

### Sorting

- Default sort: Spend descending (highest spend first) — this is the safest default that works for all clients
- Null values sort to bottom
- Clickable column headers for all sortable columns
- Sort arrow indicator: `▲` or `▼` on active column, dim `▲` on inactive sortable columns

### Sticky Columns

The `#` (index) and `Name` columns must be sticky (`position: sticky`) so they remain visible when scrolling horizontally:
- `#` column: `left: 0`, `min-width: 36px`, `z-index: 5`
- `Name` column: `left: 36px`, `min-width: 340px`, `z-index: 5`
- Both need matching `background` color on the `td` to prevent content showing through
- On `tr:hover`, update the sticky cell backgrounds to the hover color (`#1a1f2e`)
- `thead th` equivalents need higher `z-index: 11` to stay above sticky `td` cells

### Sticky Header Row

The `thead` row must be sticky (`position: sticky; top: 0`) so column headers remain visible when scrolling vertically:
```css
thead th {
  position: sticky;
  top: 0;
  z-index: 10;
  background: #151922;
}
/* Corner cells (sticky both directions) need highest z-index */
thead th.stick-idx,
thead th.stick-name {
  z-index: 15;
}
```
The table container should have `overflow-y: auto; max-height: calc(100vh - 280px)` to enable vertical scrolling within the table area while keeping the header fixed.

### Legend

Centered below subtitle, updates dynamically based on active tab:
- Shows all KPI tier thresholds with colored dots (green/yellow/red/gray)
- Video tabs additionally show Hook Rate and Hold Rate thresholds
- Format: `● CAC ≤ $1,800 | CPL ≤ $100` (green) · `● CAC $1,800–$2,500 | CPL $100–$120` (yellow) · etc.

### No Totals Row

Do not include a totals/summary row at the bottom of tables.

### View Links

The "Ad" column shows a `View ↗` link (with an external-link SVG icon) that opens the ad preview/folder in a new tab. Show `—` (em dash in muted gray) if no link is available. Link styling: `color: #60A5FA`, hover `#93C5FD`, font-family DM Sans, 11px, `display: inline-flex; align-items: center; gap: 3px`.

### Brief Links

The "Brief" column shows a `Brief ↗` link that opens the creative brief Google Doc in a new tab. This is extracted from the **Request Doc** column in the Creative Tracker xlsx files:

- **Old-format tracker** (`*Creative & Copy Tracking Sheet*`): "Request Doc" column (col 9 in Static Ad Tracker / Video Ad Tracker / Copy Tracker) — check `cell.hyperlink.target` for the Google Doc URL
- **New-format tracker** (`*Creative Tracker*` or `*2.0*`): "Request Doc" column (col M in Static Creative Tracker, col L in Video Creative Tracker) — check `cell.hyperlink.target`

**Extraction logic:**
```python
def extract_brief_links(file_path, sheet_name, name_col, request_doc_col):
    """Extract brief (Request Doc) links from tracker sheets."""
    wb = openpyxl.load_workbook(file_path, data_only=True)
    ws = wb[sheet_name]
    brief_map = {}
    for row in ws.iter_rows(min_row=2, max_row=ws.max_row):
        name = str(row[name_col-1].value or '').strip()
        if not name: continue
        cell = row[request_doc_col-1]
        url = None
        if cell.hyperlink and cell.hyperlink.target and 'http' in str(cell.hyperlink.target):
            url = cell.hyperlink.target
        elif cell.value and 'http' in str(cell.value):
            url = str(cell.value)
        if url:
            brief_map[name] = url
    wb.close()
    return brief_map
```

**Display:** Same styling as View links but with text "Brief" instead of "View". Show `—` if no brief link available. Place the Brief column immediately after the Ad (View) column.

**Data field:** Each ad entry should include a `brief` field alongside the existing `link` field:
```javascript
{name:'SC7_CheckThis | SV8_Gmail', link:'https://drive.google.com/...', brief:'https://docs.google.com/document/d/...', cost:71384, ...}
```

### Search and Filter Controls

- Search input: filters by creative name (case-insensitive), 280px wide
- Filter buttons: "Winners Only" (filter by winner status), "Min $X Spend" (configurable per client)
- Active filter buttons get highlighted styling (`background: #334155; color: #E2E8F0`)

### Number Formatting

- Dollar amounts: `$X,XXX.XX` (2 decimal places for spend, 0 for cost-per badges)
- Percentages in badges: `X.X%` (1 decimal place for Hook/Hold/Close)
- Percentages in regular cells: `X.XX%` (2 decimal places for CTR)
- Counts: Comma-separated integers (e.g., `1,234`)
- ROAS: `X.XXx` format (2 decimal places with trailing "x")
- Null/missing values: em dash `—` in muted gray (`#475569`)

## Top Ads Text Report

In addition to the dashboard, the user may request a text-based "Top Ads" summary. Format:

```
**Top Ads (date range):**

- [Creative Name](preview_link)
    - $X,XXX.XX Spent
    - N leads at $XXX.XX
    - N qualified leads at $XXX.XX
    - N MQLs at $XXX.XX
    - N SQLs at $XXX.XX
    - N customers at $XXX.XX
```

Rules:
- Only include creatives meeting the user's threshold (ask for KPI cutoff and minimum count)
- Sort by primary KPI
- Only include funnel stages with values > 0
- Hyperlink the creative name to its ad preview link
- Can also be used for roll-up reports (combining variations under a concept/body)
- When rolling up, sum cost/leads/qa/mql/sql/customer and recalculate cost-per metrics

## File Output

Save the dashboard HTML with a descriptive name including the client and date range, e.g., `clientname_dashboard_mar16_23.html`.

## Common Issues

1. **ALWAYS use Excel over CSV**: Excel (.xlsx) files preserve embedded hyperlinks that CSVs lose entirely. A single xlsx tracker file often contains BOTH performance data sheets AND tracker tabs with File Links. Always scan all xlsx files first and inspect all sheets with openpyxl before falling back to CSVs.
2. **Facebook links truncated**: Always use `\\u0026` for `&` in URLs within JS strings in HTML files
3. **Spend mismatch between variations and concepts**: Concepts are typically the source of truth; variations export may be incomplete
4. **`#DIV/0!`, `#REF!`, and `-` values**: Clean to null/`—` during parsing
5. **Overlapping date ranges**: If combining data from overlapping weekly exports, flag the overlap to the user rather than silently double-counting
6. **Browser caching**: If the user reports seeing old versions after publishing, generate with a new filename
7. **Column name variations**: Different clients may use slightly different column names — inspect headers first and adapt
8. **Naming convention differences**: Not all clients use the VHK/VB/VV system — inspect the data and adapt the link-matching logic accordingly
9. **Hyperlinks in Excel**: Cell values may show display text ("See Here") while actual URLs are embedded as hyperlinks — use `openpyxl` with `cell.hyperlink.target` to extract. **CSV exports lose all hyperlinks — NEVER use CSV for link extraction.**
10. **Two or three tracker files**: Some clients have an old tracker, a new tracker, AND a Facebook ad report — extract links from ALL sources and merge. Priority: Facebook preview links > new tracker > old tracker.
11. **Old tracker has everything**: The old-format tracker xlsx (`*Creative & Copy Tracking Sheet*`) typically has BOTH Ad Tracker tabs (with `Creative Name` + `File Link` columns) AND Performance tabs (with `Ad Name` + metrics). Always check ALL sheets.
12. **New Name column**: Old-format trackers often have a `New Name` column that maps old creative names to new names. Extract links for BOTH the old name and the new name.
13. **Concept roll-up too granular**: Old-style names often have Holiday, NY, CTA, V2/V3, Disclaimer, ARDisclaimer suffixes that should be stripped to get the core concept — use regex normalization
14. **Duplicate rows**: Some datasets have duplicate ad names — deduplicate by keeping the first occurrence
15. **Concept tabs column parity**: Concept tabs must include the same columns as variation tabs, PLUS a "# Ads" count column. Keep the Status column as well — concept tabs show BOTH Status badges AND # Ads count.
16. **Empty columns**: If a column is entirely empty/zero across all rows (e.g., Revenue, ROAS, Profit for a client that doesn't track those), omit it from the dashboard rather than showing a column of dashes
17. **Tracker name column varies by format**: Old-format uses `Creative Name` for all types. New-format uses `Static Creative Name`, `Video Creative Name`, `Copy Name` — match accordingly when extracting links.
18. **Link column varies by format**: Old-format uses `File Link` (and `File Folder`, `Performance`, `New Performance`). New-format uses `Folder Link`. Always check ALL possible column names.
19. **Google Drive / Dropbox / Facebook links**: Clients use different link types — Google Drive folder links, Google Drive file links, Dropbox links, Facebook preview links. All work as "View" links in the dashboard.
20. **Report CSV ad names include copy + date**: Report CSVs often have combined names like `SC5_Name | SV3_Visual | H- Copy Text B- Body | 01-26-2026`. Strip the copy (H-/B-) and date portions to extract the creative name for matching and roll-up.
21. **Language/agency prefixes**: Some ad names have prefixes like `Arabic |`, `BAD |`, `BAD | Spanish |`, `BAD | 7/17/2025 |` — strip these to get the core creative name.
22. **Sub-header rows in new-format tracker**: New-format trackers often have a sub-header row (row 2) with labels like "Ad Spend", "Main KPI 1". Data starts at row 3, not row 2.
