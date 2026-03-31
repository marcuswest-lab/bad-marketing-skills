---
name: brief-to-tracker
description: Extract data from a Static Copy/Creative Brief Google Doc and output tab-separated rows ready to paste into the Creative Tracker spreadsheet. Use when the user provides a creative brief link and wants tracker-ready output. Trigger phrases include "brief to tracker", "add brief to tracker", "creative brief output", "tracker entry from brief".
user_invocable: true
---

# Brief-to-Tracker: Static Creative Brief → Creative Tracker Output

Reads a Static Copy/Creative Brief (Google Doc) and outputs tab-separated plain text rows that can be pasted directly into the **Static Creative Tracker** tab of the Creative Tracker spreadsheet.

---

## When to Use

- User provides a Google Doc link to a static creative brief
- User asks to convert a brief into tracker rows
- User wants tracker-ready output from a creative brief

## How It Works

1. Open the creative brief Google Doc in the browser (use Chrome MCP tools)
2. Read the full document content by navigating through each section (OVERVIEW + each variation 1-5+)
3. Extract all relevant fields
4. Output tab-separated rows matching the Static Creative Tracker column structure

---

## Creative Brief Structure

### OVERVIEW Section (page 1-2)
Located at the top of the brief. Contains shared fields that apply to ALL variations:

| Brief Field | Description |
|---|---|
| AI Allowed? | Whether AI is allowed for this brief |
| Photo Folder | Master picture folder link |
| Reference | Reference image(s) |
| Idea Name | e.g., "Affordable Moves" |
| Angle Name | e.g., "Moving Cost Clarity" |
| Style Name | e.g., "Multi quote visual" |
| Task | Description of what to create |
| General Notes | Non-design notes |
| Design Notes | Design-specific notes |
| Link to Brand Assets | Brand assets folder link |
| Ratio Format(s) | e.g., "1x1 9x16" |
| Ad Platform | e.g., "Meta" |
| Avatar | Target audience description |
| Brand Voice | Brand voice guidelines |
| Net New/Iteration | "Net New" or "Iteration" |
| Landing Page URL | Destination URL |
| Conversion Objective | e.g., "Booked Jobs" |

### Per-Variation Sections (pages 3+)
Each variation (numbered 1, 2, 3, etc.) has its own table:

| Brief Field | Description |
|---|---|
| Copywriter | Name of the copywriter (appears once above variation tables) |
| File Name | Full creative name with CID, e.g., "Moving Cost Clarity - Multi quote visual 1 - CID65D8FUL" |
| File | Link to the file |
| Notes | General notes for this variation |
| Design Notes | Design notes for this variation |
| Variation Type | Dropdown: "Visual", "Copy", etc. |
| Awareness Level | Dropdown: "Solution Aware", "Problem Aware", "Most Aware", etc. |
| Lead Type | Dropdown: "Offer", etc. |
| Status | Dropdown: "Ready For Internal", "Testing", etc. |
| Copy | The ad copy text, or "*SAME AS REFERENCE IMAGE*" |

---

## Static Creative Tracker Column Mapping

The Static Creative Tracker tab has these columns (A through AE). Map brief fields to tracker columns as follows:

### Group 1: Columns A through N (paste starting at column A)

| Col | Tracker Header | Source | Notes |
|---|---|---|---|
| A | Created Date | Today's date | Format: M/D/YYYY |
| B | Launch Date | Leave blank | Filled later when ad launches |
| C | Static Creative Name | File Name from each variation | Direct copy from brief |
| D | Type | Net New/Iteration field | "Net New" or "Iteration" |
| E | Status | Status field from each variation | Use exact value from brief dropdown |
| F | Winner Status | Leave blank | Filled later based on performance |
| G | Platform | Ad Platform field | e.g., "Meta" |
| H | Static Format | Derive from Ratio Format | "Single Static" for standard, "Carousel" for multi-slide |
| I | Awareness Level | Awareness Level from each variation | Use exact dropdown value |
| J | Lead Type | Lead Type from each variation | Use exact dropdown value |
| K | Variation Type | Variation Type from each variation | Use exact dropdown value |
| L | CID | Extract from File Name | The alphanumeric code after "CID" at end of File Name |
| M | Request Doc | Brief Google Doc URL | The URL the user provided |
| N | Folder Link | Photo Folder or File link | From overview or per-variation |

### Group 2: Columns O-V (SKIP - performance data, do not fill)

These columns contain performance metrics (Ad Spend, ROAS, etc.) and are either auto-calculated or filled later. Leave blank.

### Group 3: Columns W through AC (paste starting at column W)

| Col | Tracker Header | Source | Notes |
|---|---|---|---|
| W | Idea Name | Idea Name from OVERVIEW | Same for all variations in this brief |
| X | Angle Name | Angle Name from OVERVIEW | Same for all variations |
| Y | Image Style | Style Name from OVERVIEW | Same for all variations, e.g., "Multi quote visual" |
| Z | Slide Count | Leave blank | Unless carousel, then count of slides |
| AA | Copywriter | Copywriter field | Usually same for all variations |
| AB | Designer | Leave blank | Not typically in the brief; filled by design team |
| AC | Variation Name | Construct from File Name | The descriptive part, e.g., "Multi quote visual 1" |

### Columns AD-AE (leave blank)

---

## Output Format

Generate TWO paste-ready blocks per batch of variations:

### Block 1: Columns A through N
One row per variation, tab-separated. Output as a code block the user can copy.

Format per row:
```
{Created Date}\t{Launch Date}\t{Static Creative Name}\t{Type}\t{Status}\t{Winner Status}\t{Platform}\t{Static Format}\t{Awareness Level}\t{Lead Type}\t{Variation Type}\t{CID}\t{Request Doc URL}\t{Folder Link}
```

### Block 2: Columns W through AC
One row per variation, tab-separated. Output as a separate code block.

Format per row:
```
{Idea Name}\t{Angle Name}\t{Image Style}\t{Slide Count}\t{Copywriter}\t{Designer}\t{Variation Name}
```

### Important Output Rules

1. **Use TAB characters** between fields (not spaces or pipes)
2. **Leave blank fields as empty** (just consecutive tabs: `\t\t`)
3. **Do NOT include headers** in the paste blocks - just data rows
4. **CID extraction**: From "Moving Cost Clarity - Multi quote visual 1 - CID65D8FUL", extract just "65D8FUL" (everything after "CID")
5. **Variation Name extraction**: From "Moving Cost Clarity - Multi quote visual 1 - CID65D8FUL", extract "Multi quote visual 1" (the style + number portion)
6. **Date format**: Use M/D/YYYY (e.g., 3/26/2026)
7. **Status mapping**: Use the brief's status value directly (e.g., "Ready For Internal" stays as-is)
8. **Static Format**: Default to "Single Static" unless the brief indicates carousel or multi-format

---

## Step-by-Step Extraction Process

1. **Open the brief URL** in Chrome using MCP navigate tool
2. **Read the OVERVIEW section** - extract: Idea Name, Angle Name, Style Name, Ad Platform, Net New/Iteration, Photo Folder
3. **Read the section between Overview and variations** - extract: Ratio Format(s), Ad Platform, Net New/Iteration, Landing Page URL, Copywriter
4. **For each variation (1, 2, 3, etc.)**, click the numbered heading in the sidebar and extract: File Name, Variation Type, Awareness Level, Lead Type, Status, Copy
5. **Parse the CID** from each File Name (text after "CID" at the end)
6. **Construct the Variation Name** from each File Name (the style/descriptor + number before the CID portion)
7. **Output the two paste blocks** as code blocks

### Reading the Document
- Use the document sidebar (left panel) to navigate between OVERVIEW and numbered variations
- Use `get_page_text` or `read_page` to extract text content
- For each variation, click its number in the sidebar, wait for navigation, then screenshot/read the content
- The Copywriter field appears at the top of the variation pages, above the first variation table

---

## Example

Given a brief with:
- Idea Name: Affordable Moves
- Angle Name: Moving Cost Clarity
- Style Name: Multi quote visual
- Ad Platform: Meta
- Net New/Iteration: Iteration
- Copywriter: Nate Weeks
- Variation 1: File Name "Moving Cost Clarity - Multi quote visual 1 - CID65D8FUL", Visual, Solution Aware, Offer, Ready For Internal

### Block 1 output (paste at A of first empty row):
```
3/26/2026		Moving Cost Clarity - Multi quote visual 1 - CID65D8FUL	Iteration	Ready For Internal		Meta	Single Static	Solution Aware	Offer	Visual	65D8FUL	https://docs.google.com/document/d/...
```

### Block 2 output (paste at W of same row):
```
Affordable Moves	Moving Cost Clarity	Multi quote visual		Nate Weeks		Multi quote visual 1
```
