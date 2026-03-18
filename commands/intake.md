# Intake Command

Process new file entry, auto-categorize or ask for direction.

## Trigger Words

`intake`, `categorize`, `file intake`, `process new files`, `sort files`

## Precondition

`{TARGET}` path must be determined

## File Categories

### Claude Can Read (Auto-Categorizable)

| Category | Extensions |
|----------|------------|
| Markup | `.md`, `.markdown`, `.rst`, `.org` |
| Plain Text | `.txt`, `.text`, `.log` |
| Data | `.json`, `.yaml`, `.yml`, `.xml`, `.toml`, `.csv`, `.tsv` |
| Code | `.py`, `.js`, `.ts`, `.java`, `.c`, `.cpp`, `.h`, `.go`, `.rs`, `.rb`, `.php`, etc. |
| Web | `.html`, `.htm`, `.css`, `.scss`, `.sass`, `.less` |
| Config | `.env`, `.ini`, `.cfg`, `.conf`, `.properties` |
| Other | `.tex`, `.rtf` |

### Claude Cannot Read (Manual Required → `_unsupported/`)

| Category | Extensions |
|----------|------------|
| PDF | `.pdf` |
| Office | `.docx`, `.xlsx`, `.pptx`, `.doc`, `.xls`, `.ppt` |

### Ignored Files (Keep Original Location)

These files are **completely ignored** - never moved or tracked:

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
  ├── Can read → Analyze filename + content → Match directories
  │     ├── Match found → Suggest move to X directory
  │     └── No match → Ask user OR suggest new category
  └── Cannot read → Move to _unsupported/ → User manual process
        └── User done → File leaves _unsupported/, no longer tracked
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
1. **Filename** - extract keywords
2. **Content** - read first 10 lines for context
3. **Extension** - determine file type
4. **Match** - apply keyword rules + extension rules

### Step 4: Handle Unsupported Files

Move to `{TARGET}/_unsupported/`:
- These files need manual review
- User decides final location
- Once moved out, no longer tracked by skill

### Step 5: Output Report

```markdown
## Readable Files (X) - Auto-categorization

| File | Analysis | Suggested Action |
|------|----------|------------------|
| report.md | Keyword: demand | → knowledge/demand-mining/ |
| data.csv | Tabular data | → projects/analytics/ |

## Unsupported Files (X) - Manual Required

| File | Type | Reason |
|------|------|--------|
| report.pdf | pdf | Cannot read content |
| data.xlsx | xlsx | Cannot read content |

## Confirmation
Execute move operations? (yes/no/select)
```

### Step 6: Execute Moves

1. Move auto-categorized files to suggested locations
2. Move unsupported files to `{TARGET}/_unsupported/`
3. Update report with final status

## Auto-Categorization Rules (Readable Files)

### By Keyword (Primary)

| Keywords | Target Directory |
|----------|-----------------|
| demand, mining, 挖掘 | `{TARGET}/knowledge/demand-mining/` |
| demand, mining, business, 商业 | `{TARGET}/knowledge/business-demand-mining/` |
| progress, daily, 周报, 日报 | `{TARGET}/knowledge/progress/` |
| weekly, 周报 | `{TARGET}/knowledge/progress/weekly/` |
| monthly, 月报 | `{TARGET}/knowledge/progress/monthly/` |
| brainstorm, idea, 创意, 脑暴 | `{TARGET}/knowledge/brainstorm/` |
| competitor, research, 竞品, 调研 | `{TARGET}/knowledge/competitor-research/` or `{TARGET}/projects/{project}/` |
| methodology, 方法论 | `{TARGET}/knowledge/methodology/` |
| reading, book, notes, 阅读 | `{TARGET}/knowledge/reading/` |
| agent name (scaptain/flowstage/xhs) | `{TARGET}/knowledge/agents/{agent}/` |
| product, feature, PRD | `{TARGET}/knowledge/products/` |
| marketing, promotion, 营销 | `{TARGET}/knowledge/marketing/` |
| private, secret, 个人 | `{TARGET}/private/` |

### By Extension (Fallback)

| Extension | Target Directory |
|-----------|-------------------|
| `.md`, `.markdown`, `.rst`, `.org` | `knowledge/` |
| `.txt`, `.text`, `.log` | `knowledge/` or `projects/` |
| `.csv`, `.tsv` | `projects/analytics/` or `knowledge/data/` |
| `.json`, `.yaml`, `.yml`, `.toml` | `projects/` or `knowledge/config/` |
| `.py`, `.js`, `.ts` | `projects/code/` or `agents/{name}/` |
| `.html`, `.css`, `.scss` | `projects/web/` |
| `.env`, `.ini`, `.cfg` | `projects/config/` |

## Unsupported Files Handling

### Directory

```
{TARGET}/_unsupported/
├── README.txt      # Instructions for manual processing
└── [unsupported files...]
```

### README.txt Content

```
These files could not be automatically processed because Claude cannot read their content.

Please manually:
1. Review each file
2. Move to appropriate directory
3. Once moved out of _unsupported/, these files will no longer be tracked

Supported types that were moved here:
- .pdf (binary document)
- .docx, .xlsx, .pptx (modern Office)
- .doc, .xls, .ppt (legacy Office)
```

### Important Note

> **Once files leave _unsupported/, they are no longer tracked by doctidy.**
> The skill will not remind you about these files again. Please move them to permanent locations manually.

## When Categorization Fails (Readable Files)

If no match found, ask user:

```
Cannot categorize `notes.txt`. Choose target:
1. knowledge/
2. projects/
3. Create new directory: [input name]
4. Leave in _inbox/pending/ for now
```

## Output Format

```markdown
# Intake Report
Target: {TARGET}
Date: YYYY-MM-DD

## Auto-Categorized Files (X)

| File | Type | Analysis | Action |
|------|------|----------|--------|
| file1.md | markdown | Keywords: X | → path/ |
| file2.csv | csv | Data file | → projects/ |

## Unsupported Files (X) - Manual Required

| File | Type | Reason |
|------|------|--------|
| file3.pdf | pdf | Content not readable |
| file4.xlsx | xlsx | Content not readable |

## Summary
- Auto-categorized: X
- Manual required: X

## Confirmation
Move auto-categorized files and unsupported files to _unsupported/? (yes/no/select)
```

## Special Handling

### Poor Filenames (Readable Files)
If filename is non-descriptive (e.g., `新建文件 (1).txt`, `untitled.md`):
- Ask user to rename before categorizing

### Agent-Created Files
If file is created by agent (detected from path or metadata):
- Keep in agent's working directory
- Suggest appropriate subdirectory

### Duplicate Names
If target location has file with same name:
- Suggest renaming strategy
- Ask user preference
