# Intake Command

Process new file entry, auto-categorize based on content analysis.

## Trigger Words

`intake`, `categorize`, `file intake`, `process new files`, `sort files`

## Precondition

`{TARGET}` path must be determined

## Core Philosophy

**Content-based, not folder-based.** The skill analyzes WHAT a file IS (content, type, topic) and finds the best matching location in your existing folder structure. It does NOT enforce any predefined folder names.

## File Categories

### Claude Can Read (Content Analysis Available)

| Category | Extensions |
|----------|------------|
| Markup | `.md`, `.markdown`, `.rst`, `.org` |
| Plain Text | `.txt`, `.text`, `.log` |
| Data | `.json`, `.yaml`, `.yml`, `.xml`, `.toml`, `.csv`, `.tsv` |
| Code | `.py`, `.js`, `.ts`, `.java`, `.c`, `.cpp`, `.h`, `.go`, `.rs`, `.rb`, `.php`, etc. |
| Web | `.html`, `.htm`, `.css`, `.scss`, `.sass`, `.less` |
| Config | `.env`, `.ini`, `.cfg`, `.conf`, `.properties` |
| Other | `.tex`, `.rtf` |

### Claude Cannot Read (Manual Required)

| Category | Extensions | Destination |
|----------|------------|-------------|
| PDF | `.pdf` | `_unsupported/` |
| Office | `.docx`, `.xlsx`, `.pptx`, `.doc`, `.xls`, `.ppt` | `_unsupported/` |

### Ignored Files (Keep Original Location)

These files are **never moved or tracked**:

| Category | Extensions |
|----------|------------|
| Images | `.png`, `.jpg`, `.jpeg`, `.gif`, `.bmp`, `.svg`, `.webp`, `.ico` |
| Video | `.mp4`, `.mov`, `.avi`, `.mkv`, `.webm` |
| Audio | `.mp3`, `.wav`, `.flac`, `.aac`, `.ogg` |
| Archives | `.zip`, `.tar`, `.gz`, `.rar`, `.7z` |
| Other | `.exe`, `.dmg`, `.app` |

## Core Flow

```
New File → Check readability
  ├── Can read → Analyze content + context → Match to existing folders
  │     ├── Match found → Suggest move to matching folder
  │     └── No match → Ask user for direction
  └── Cannot read → Move to _unsupported/ → User manual process
```

## Execution Steps

### Step 1: Scan Inbox

```bash
ls -la {TARGET}/_inbox/pending/
```

### Step 2: Categorize by Readability

Separate files into:
1. **Readable files** - can analyze content
2. **Unsupported files** - cannot read content

### Step 3: Analyze Readable Files

For each readable file:
1. **Read content** - analyze the actual content (first 20 lines)
2. **Extract keywords** - what topics/subjects are mentioned
3. **Determine type** - note, code, config, data, etc.
4. **Find similar files** - look for files with similar content/type in your folders
5. **Match to location** - find best existing folder match

### Step 4: Content Analysis Algorithm

```
For each file in _inbox/pending/:

1. If extension is code-like (.py, .js, .ts, etc.)
   → Look for folders containing similar code files
   → Suggest moving to the matching code folder

2. If extension is data (.csv, .json, .yaml, etc.)
   → Look for folders containing similar data files
   → Suggest moving to the matching data folder

3. If extension is config (.env, .ini, etc.)
   → Look for project folders that might need this config
   → Suggest moving near related project

4. If extension is markup/text (.md, .txt, etc.)
   → Read content, extract topics
   → Look for folders with similar topic files
   → Match by keyword similarity
```

### Step 5: Handle Unsupported Files

Move to `{TARGET}/_unsupported/`:
- These files need manual review
- User decides final location
- Once moved out, no longer tracked by skill

### Step 6: Output Report

```markdown
## Intake Report
Target: {TARGET}
Date: YYYY-MM-DD

### Readable Files (X) - Content Analysis

| File | Type | Analysis | Suggested Location |
|------|------|----------|-------------------|
| design.md | markdown | UI/UX notes | → ~/docs/notes/ui-design/ (similar files exist) |
| api.py | python | API code | → ~/docs/projects/web-api/ (same project) |
| data.csv | csv | Sales data | → ~/docs/analytics/ (similar files exist) |

### Unsupported Files (X) - Manual Required

| File | Type | Action |
|------|------|--------|
| report.pdf | pdf | → _unsupported/ (manual review needed) |
| budget.xlsx | xlsx | → _unsupported/ (manual review needed) |

### Confirmation
Execute moves? (yes/no/select)
```

### Step 7: Execute Moves

1. Move auto-categorized files to suggested locations
2. Move unsupported files to `{TARGET}/_unsupported/`
3. Update report with final status

## When No Match Found

If content analysis finds no suitable existing folder:

```
Cannot find similar files/folders for `notes.txt`.

Options:
1. Create new folder: [input folder name]
2. Choose existing folder:
   [list of top-level folders in {TARGET}]
3. Leave in _inbox/pending/ for now
```

## Auto-Categorization Examples

### Example 1: Code File

```
File: auth.py
Content: Python code with JWT authentication
Analysis: Authentication module
Result: Find ~/projects/web-app/ with similar Python files → Suggest: ~/projects/web-app/auth.py
```

### Example 2: Data File

```
File: customers-2026.csv
Content: CSV with customer data (name, email, purchase history)
Analysis: Customer data/spreadsheet
Result: Find ~/analytics/customer-data/ with similar CSVs → Suggest: ~/analytics/customer-data/
```

### Example 3: Notes

```
File: meeting-notes.md
Content: Meeting notes about Q2 planning, mentions "marketing", "launch"
Analysis: Meeting notes about marketing campaign
Result: Find ~/documents/meetings/campaign-planning/ with similar meeting notes → Suggest that folder
```

### Example 4: No Match Found

```
File: random-thoughts.txt
Content: Personal notes, mixed topics
Analysis: General notes, no clear category
Result: No similar files found → Ask user where to place
```

## Keyword-Based Fallback

When content analysis is inconclusive, use filename keywords:

| Keywords | Meaning |
|----------|---------|
| config, setting, env | Configuration file |
| draft, note, idea, thought | Notes/drafts |
| report, summary, overview | Reports |
| code, src, module | Source code |
| data, csv, stats | Data files |
| meeting, minutes | Meeting notes |
| todo, task, checklist | Task lists |

## Important Notes

1. **No enforced structure** - The skill adapts to YOUR folder organization
2. **Content first** - Analyzes actual file content, not just filename
3. **Similarity matching** - Finds folders with similar existing files
4. **User always decides** - Can override any suggestion

## Output Format

```markdown
# Intake Report
Target: {TARGET}
Date: YYYY-MM-DD

## Auto-Categorized (X files)

| File | Analysis | Destination |
|------|----------|-------------|
| file1.md | Topic: X, Type: note | → ~/docs/folder/ |

## Manual Review Required (X files)

| File | Type | Location |
|------|------|----------|
| file2.pdf | pdf | _unsupported/ |

## Summary
- Processed: X
- Auto-categorized: X
- Manual required: X
```
