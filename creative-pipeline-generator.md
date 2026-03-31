---
name: creative-pipeline-generator
description: Generate meeting notes showing which ads are in the testing pipeline vs production pipeline from a Creative Tracker spreadsheet (.xlsx). Use this skill whenever the user uploads a Creative Tracker spreadsheet and asks for pipeline status, meeting notes, creative pipeline update, testing/production pipeline summary, or anything related to sorting ads into testing vs production categories. Also trigger when the user mentions "pipeline notes", "creative tracker update", "what's in testing", "what's in production", or references updating meeting notes from a creative tracker file. This skill reads the Video Creative Tracker and Static Creative Tracker sheets, categorizes ads based on folder link and launch date presence, groups them by concept/angle, extracts Google Drive hyperlinks, and outputs formatted meeting notes.
---

# Creative Pipeline Generator

This skill reads a Creative Tracker spreadsheet and generates formatted meeting notes that categorize ads into **Testing pipeline** and **Production pipeline** sections, split by Statics and Videos.

## Classification Logic

The two key signals are the **Folder Link** column and the **Launch Date** column:

- **Testing pipeline**: The ad has a Folder Link value (non-empty, not "0") but does NOT have a Launch Date. This means the creative assets are ready/in-progress but haven't been launched yet.
- **Production pipeline**: The ad has a name on the tracker but does NOT have a Folder Link and does NOT have a Launch Date. This means the brief exists but creative assets haven't been started yet.
- **Already launched / active**: If an ad has a Launch Date, it's already live — skip it entirely. It doesn't belong in either pipeline section.

## Exclusion Rules

- **Hard-excluded concepts (always skip every row whose name starts with any of these, regardless of status or pipeline stage):**
    - "Wife Won't Kiss You"
    - "Heart3Heart"
  These are legacy template/example rows and must NEVER appear in any pipeline output.
- Skip any other rows that are clearly example/template rows (e.g., rows with "00 - Copy Submission Docs" as their folder value with no hyperlink). If in doubt, ask the user.
- Skip rows with no ad name.
- Skip rows where Status is "Winner", "Loser", or similar terminal statuses AND they have a launch date — these are done.

## Reading the Spreadsheet

Use `openpyxl` (not pandas) to read the file, because you need to extract **hyperlinks** from cells, not just cell values.

### Sheet and Column Mapping

**Video Creative Tracker** sheet:
- Col B (2): Launch Date
- Col C (3): Video Creative Name
- Col E (5): Status
- Col L (12): Request Doc (has hyperlinks for production items)
- Col M (13): Folder Link (has hyperlinks — these are the Google Drive folder links)

**Static Creative Tracker** sheet:
- Col B (2): Launch Date
- Col C (3): Static Creative Name
- Col E (5): Status
- Col M (13): Request Doc (has hyperlinks for production items)
- Col N (14): Folder Link (has hyperlinks — these are the Google Drive folder links)

Data rows start at row 3 (rows 1-2 are headers/subheaders). Row 3 may also be a CID-only row — skip it if the name cell is empty or not a string.

### Extracting Hyperlinks

For each cell in the Folder Link column, check `cell.hyperlink.target` to get the actual Google Drive URL. The cell's display value (e.g., "3/18/2026 | Full Service Moving Feature Stack") is used as the link text.

```python
import openpyxl

wb = openpyxl.load_workbook('tracker.xlsx')

# For each tracker sheet:
ws = wb['Video Creative Tracker']
for row in range(3, ws.max_row + 1):
    name = ws.cell(row, 3).value  # ad name
    launch = ws.cell(row, 2).value  # launch date
    folder_cell = ws.cell(row, 13)  # folder link cell (col M for video, col N for static)
    folder_val = folder_cell.value
    folder_url = folder_cell.hyperlink.target if folder_cell.hyperlink else None
    
    has_folder = folder_val and str(folder_val).strip() not in ['0', '', 'nan']
    has_launch = launch is not None
```

## Grouping Items

Group individual ad variations by their concept/angle name. For example, if there are 5 variations of "Moving Cost Clarity - Offer Lead 1" through "Offer Lead 5" that all share the same folder link, group them as a single line item like "Moving Cost Clarity - Multi quote animation" (using the folder label to describe the batch).

For testing pipeline items, use the folder link display text as the descriptor. For production pipeline items, use the concept/angle name from the ad names.

When multiple ads share the same folder link URL, they belong to the same batch and should be consolidated into one line.

## Output Format

Output formatted text that matches this structure exactly. Use markdown with bullet points, bold headers, and hyperlinked folder names using the 📁 emoji:

```
**Testing pipeline:**
- Statics:
    - [Concept Name] - [Description]
        - 📁 [Folder Display Text](https://drive.google.com/drive/folders/...)
    - [Another Concept]
        - 📁 [Folder Display Text](https://drive.google.com/drive/folders/...)
- Videos:
    - [Concept Name] - [Description]
        - 📁 [Folder Display Text](https://drive.google.com/drive/folders/...)

**In the production pipeline:**
- Statics:
    - [Concept Name] - [Description]
    - [Another Concept] - [Description]
- Videos:
    - [Concept Name] - [Description]
    - [Another Concept] - [Description]
```

Key formatting rules:
- Testing pipeline items get a 📁 emoji with a hyperlinked folder name on a sub-bullet beneath the concept name.
- Production pipeline items are just plain text names with no links.
- If a category (Statics or Videos) has no items, show "*(none currently)*".
- Group by concept — don't list every individual variation.
- The output should be concise and scannable, suitable for pasting directly into meeting notes.
