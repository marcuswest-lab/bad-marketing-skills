---
name: top-ads-report
description: "Generate a Top Ads report for weekly meeting notes from a Creative Data xlsx file. Use this skill when the user asks for top ads, top performers, weekly winners, meeting notes with top creatives, or wants to pull the best-performing ads from a creative data export. Trigger phrases: 'top ads', 'top performers', 'weekly winners', 'best ads this week', 'meeting notes top ads', 'pull the winners'. The skill reads a Creative Data xlsx, filters to creatives with CPQA under a threshold, matches ad preview links from Creative Tracker xlsx files and Facebook CSV reports, and outputs hyperlinked formatted text."
---

# Top Ads Report Generator

## Overview

This skill generates a formatted "Top Ads" section for weekly meeting notes. It reads a weekly Creative Data xlsx export, filters to top-performing video and static ad creatives (excluding copy and concept-level data), matches hyperlinks from Creative Tracker files, and outputs markdown with hyperlinked ad names and funnel metrics.

## Required Inputs

1. **Creative Data xlsx** — A weekly export with sheets like "Videos", "Statics", "Copy", "Static Concepts", "Video Concepts". The user will specify the file path or it can be found in the client folder.
2. **CPQA threshold** — The maximum CPQA to qualify as a "top ad". Ask the user if not specified. Common thresholds:
   - TCC: $450
   - VAM: varies by client
3. **Client name** — To find the correct Creative Tracker files for link matching.
4. **Date range** — For the report header (e.g., "3/23 – 3/29").

## Data Sources for Links (check ALL of these)

Links are matched from multiple sources in priority order:

1. **Facebook Ad Spend/Click CSVs** — Files like `TCC-Ad-spend-click-data*.csv` or `Untitled-report-*.csv` in the client folder. These have a `Preview link` column with Facebook ad preview URLs. Use the most recent file.
2. **New Creative Tracker xlsx** — Files like `[Client 2.0] - Creative Tracker.xlsx`. Has "Static Creative Tracker" (name col C, Folder Link col N) and "Video Creative Tracker" (name col C, Folder Link col M). Use openpyxl to extract hyperlinks from cells.
3. **Old Creative Tracker xlsx** — Files like `[Client] - Creative & Copy Tracking Sheet*.xlsx`. Has "Static Ad Tracker" and "Video Ad Tracker" sheets with "Creative Name" and link columns ("Performance", "New Performance", "File Link"). Also has a "New Name" column that maps old names to new names — extract links for both.

Always use openpyxl for xlsx files to preserve embedded hyperlinks.

## Processing Steps

### Step 1: Parse the Creative Data xlsx

```python
import openpyxl, os

def parse_val(v):
    if v is None: return None
    s = str(v).replace('$','').replace(',','').strip()
    if s in ('', '#DIV/0!', '#REF!', '#N/A', 'nan', '-'): return None
    try: return float(s)
    except: return None
```

- Open the xlsx with openpyxl (data_only=True)
- Read ONLY the "Videos" and "Statics" sheets — skip Copy, Concepts, and any other sheets
- Use header-based column lookup (find columns by name, not hardcoded index)
- Key columns to find: Name, Cost, Stage: Lead (or Leads), cost per stage: lead (or CPL), Stage: Qualified Application, CPQA, Stage: Marketing Qualified Lead, CPMQL, Stage: Sales Qualified Lead, CPSQL, Stage: Customer, CAC
- Filter to rows where Cost > 0 AND CPQA is not null AND CPQA <= threshold
- Skip test/example rows (e.g., SC1_9t5, SV1_beach1)
- Sort results by CPQA ascending (best performers first)

### Step 2: Extract Links

Extract links from all available sources in the client folder:

```python
# Facebook CSV links
import csv
def extract_fb_links(csv_path):
    links = {}
    with open(csv_path, encoding='utf-8-sig') as f:
        reader = csv.DictReader(f)
        for row in reader:
            name = (row.get('Ad name') or row.get('\ufeffAd name') or '').strip()
            preview = (row.get('Preview link') or '').strip()
            if name and preview and 'http' in preview:
                links[name] = preview
    return links

# Tracker xlsx links (use openpyxl)
def extract_tracker_links(xlsx_path, sheet_name, name_col, link_cols):
    wb = openpyxl.load_workbook(xlsx_path)
    ws = wb[sheet_name]
    links = {}
    headers = {}
    for cell in ws[1]:
        if cell.value: headers[str(cell.value).strip()] = cell.column

    nc = headers.get(name_col, name_col) if isinstance(name_col, str) else name_col
    new_name_col = headers.get('New Name')

    for r in range(2, ws.max_row + 1):
        n = str(ws.cell(r, nc).value or '').strip()
        if not n: continue
        for lc_name in link_cols:
            if lc_name in headers:
                cell = ws.cell(r, headers[lc_name])
                if cell.hyperlink and cell.hyperlink.target and 'http' in str(cell.hyperlink.target):
                    links[n] = cell.hyperlink.target
                    if new_name_col:
                        nn = str(ws.cell(r, new_name_col).value or '').strip()
                        if nn and nn != 'nan': links[nn] = cell.hyperlink.target
                    break
    wb.close()
    return links
```

Merge all link sources — Facebook preview links take priority over tracker links.

### Step 3: Match Links to Ads

```python
def find_link(ad_name, all_links):
    # Exact match
    if ad_name in all_links: return all_links[ad_name]
    # Stripped match
    for k, v in all_links.items():
        if ad_name.replace(' ','') == k.replace(' ',''): return v
    # Pipe-delimited partial match
    parts = [p.strip() for p in ad_name.split('|')]
    for k, v in all_links.items():
        if all(p in k for p in parts if len(p) > 3): return v
    return None
```

### Step 4: Format Output

Output markdown in this exact format:

```
**Top Ads (date_range):**

- [Ad Name](link_url)
    - $X,XXX.XX Spent
    - N qualified leads at $XXX.XX
    - N MQLs at $XXX.XX
    - N SQLs at $XXX.XX
    - N customers at $XXX.XX
```

**Formatting rules:**
- Ad name is hyperlinked with the matched preview/folder link
- If no link is found, show the name without a hyperlink
- Include "Spent" on the first line, always
- Include "leads at $X" only if leads > 0 (using "leads", not "new leads")
- Include "qualified leads at $X" only if QA > 0
- Include "MQLs at $X" only if MQL > 0
- Include "SQLs at $X" only if SQL > 0
- Include "customers at $X" only if Customer > 0
- Sort by CPQA ascending (best first)
- Dollar amounts use comma separators and 2 decimal places
- Counts are integers
- Only include Videos and Statics — NO copy, NO concepts

## Client-Specific Notes

Different clients have different:
- CPQA thresholds (TCC: $450, VAM: varies)
- Tracker file naming conventions
- Column names in the data export
- Funnel stages (some clients use "Connected Calls" instead of "MQLs", etc.)

Always inspect the actual column headers before parsing. Ask the user for the CPQA threshold if not obvious from the file name or context.

## Common Issues

1. **Duplicate entries** — The xlsx may have the same ad in both a variation sheet and a concept sheet. Only read "Videos" and "Statics" sheets to avoid duplicates.
2. **#DIV/0! values** — Convert to null, skip these rows for CPQA filtering.
3. **Test rows** — Skip SC1_9t5, SV1_beach1, and similar placeholder/example rows.
4. **$ in file paths** — When constructing paths to tracker files with $ in their names, use os.path.join() to avoid shell escaping issues.
5. **Missing links** — Not all ads will have links. Show the name without hyperlink if no match is found.
6. **Column name variations** — "Stage: Lead" vs "Leads", "cost per stage: lead" vs "CPL" — always do header-based lookup, not hardcoded column indices.
