# Entropy Control Rules

Core rules to prevent document repository entropy and maintain order.

---

## File Readability

### Claude Can Read (Auto-Categorizable)

| Category | Extensions |
|----------|------------|
| Markup | `.md`, `.markdown`, `.rst`, `.org` |
| Plain Text | `.txt`, `.text`, `.log` |
| Data | `.json`, `.yaml`, `.yml`, `.xml`, `.toml`, `.csv`, `.tsv` |
| Code | `.py`, `.js`, `.ts`, `.java`, `.c`, `.cpp`, `.go`, `.rs`, `.rb`, `.php`, etc. |
| Web | `.html`, `.htm`, `.css`, `.scss`, `.sass`, `.less` |
| Config | `.env`, `.ini`, `.cfg`, `.conf`, `.properties` |
| Other | `.tex`, `.rtf` |

### Claude Cannot Read (Manual Required → `_unsupported/`)

| Category | Extensions |
|----------|------------|
| PDF | `.pdf` |
| Office (modern) | `.docx`, `.xlsx`, `.pptx` |
| Office (legacy) | `.doc`, `.xls`, `.ppt` |

### Ignored Files (Keep Original Location)

Files are **completely ignored** - no tracking, no categorization, keep as-is:

| Category | Extensions |
|----------|------------|
| Images | `.png`, `.jpg`, `.jpeg`, `.gif`, `.bmp`, `.svg`, `.webp`, `.ico` |
| Video | `.mp4`, `.mov`, `.avi`, `.mkv`, `.webm` |
| Audio | `.mp3`, `.wav`, `.flac`, `.aac`, `.ogg` |
| Archives | `.zip`, `.tar`, `.gz`, `.rar`, `.7z` |
| Other | `.exe`, `.dmg`, `.app` |

> **Images, videos, audio, and archives are never moved or tracked.**

---

## Golden Rules

### 1. New Files to Inbox

**Rule**: All new text files should go to `{TARGET}/_inbox/pending/`

**Why**: Centralized entry point makes categorization easier. Files in proper locations are harder to track and organize.

**For Agents**:
```
Agent creates new file → Move to {TARGET}/_inbox/pending/
```

### 2. Trash Before Delete

**Rule**: Never delete files directly. Move to `{TARGET}/_trash/` first.

**Why**: Mistakes happen. Trash provides recovery window.

**Deletion Policy**:
| Location | Auto-clean after |
|-----------|------------------|
| `{TARGET}/_trash/` | 30 days |

**Exceptions**:
- Truly temporary files (can delete immediately)
- Files already in trash > 30 days

### 3. No Scattering

**Rule**: No text files in root directory or outside proper categories.

**Allowed Locations**:
- `{TARGET}/_inbox/pending/` (staging)
- `{TARGET}/_trash/` (deleted staging)
- `{TARGET}/_unsupported/` (manual processing - temporary)
- Category directories (knowledge/, projects/, etc.)

**Penalty**: Scattered files reduce entropy control score.

### 4. Unsupported Files → Manual Processing

**Rule**: Files that Claude cannot read go to `{TARGET}/_unsupported/`

**Process**:
1. File enters `_unsupported/`
2. User manually reviews and moves to appropriate location
3. Once moved out, file is **no longer tracked** by doctidy

**Why**: Claude cannot analyze content of these files, so intelligent categorization is impossible.

### 5. Regular Cleanup

**Schedule**:
- **Weekly**: Quick status check
- **Monthly**: Full score and cleanup
- **Quarterly**: Deep analyze and restructure if needed

### 6. Duplicate Detection

**Rule**: When duplicates found, process promptly.

**Duplicate Definition**: Files with same basename AND same readable type. `report.md` and `report.pdf` are NOT duplicates (different types, one is readable, one is not).

**Options**:
1. Keep readable format (.md), move others to trash
2. Merge content if complementary
3. Ask user to decide

**Priority for Keeping**:
1. User-specified preference
2. Readable file over non-readable
3. Business-relevant directory
4. Most recently modified

---

## Path Caching

To avoid repeated path specification, doctidy caches the target path.

### Implementation

1. **First Use**: When user specifies a path, store it in `_index/registry.yaml`:
```yaml
cachedTarget: "/path/to/docs"
lastUsed: YYYY-MM-DD
```

2. **Subsequent Uses**: Read from `_index/registry.yaml` instead of prompting

3. **Override**: User can always specify a different path:
```bash
/doctidy "score /new/path"
```

4. **Invalidation**: User can reset:
```bash
/doctidy "set target to /new/path"
```

### Safety

- Path caching is optional - always respect user-specified paths
- If `_index/` is deleted, cache is lost (safe behavior)
- Git commit before path changes for recovery

---

## Directory Conventions

### Standard Structure

```
{TARGET}/
├── _index/           # Index and config
│   ├── registry.yaml
│   └── score.md
├── _inbox/           # Entry point
│   └── pending/
├── _trash/           # Deletion staging (30-day retention)
├── _unsupported/     # Manual processing required
│   └── README.txt    # Instructions for manual review
├── knowledge/        # Knowledge base
│   ├── agents/       # AI agent directories
│   ├── reading/
│   ├── progress/
│   ├── methodology/
│   └── ...
├── projects/         # Project docs
└── private/          # Sensitive data
```

### Naming Conventions

| Type | Convention | Example |
|------|------------|---------|
| Directories | kebab-case or CN | `demand-mining`, `需求挖掘` |
| Files | kebab-case + date | `progress-2026-03.md` |
| Dates | ISO 8601 | `YYYY-MM-DD` |
| Agent dirs | lowercase | `flowstage`, `scaptain` |

### Language Consistency

**Option A**: All English
- Pro: Consistent, developer-friendly
- Con: May feel unnatural for Chinese content

**Option B**: All Chinese
- Pro: Natural for Chinese content
- Con: Harder for international collaboration

**Option C**: Mixed (current recommendation)
- Category names: Chinese (familiar)
- File names: English or descriptive
- Agent names: lowercase English

---

## For AI Agents

### Agent Workflow

```
1. Read {TARGET}/README.md (if exists)
2. Read {TARGET}/_index/registry.yaml (if exists)
3. Check _inbox/pending/ for new files
4. Do work
5. Save output to appropriate location
6. If unsure → _inbox/pending/
7. If output is non-readable (.pdf, .docx, etc.) → _unsupported/
```

### Agent Responsibilities

- Create files in proper locations
- Use readable formats when possible (.md over .docx)
- Put non-readable outputs to `{TARGET}/_unsupported/`
- Update registry if modifying structure
- Report issues via score command
- Follow entropy control rules

---

## Recovery Procedures

### Accidental Deletion

1. Check `{TARGET}/_trash/`
2. If not found, check git history
3. Restore from backup if available

### Wrong Move

1. Check source location in git history
2. Move back to original location
3. Update registry if needed

### Index Corruption

1. Run `analyze` to get current state
2. Run `organize` to fix issues
3. Regenerate registry.yaml

---

## Important Notes

### About Unsupported Files

> **Files in `_unsupported/` are NOT tracked after user moves them.**
>
> The skill will:
> - Move non-readable files to `_unsupported/`
> - Create a README.txt with instructions
> - NOT remind you about these files again
>
> **User responsibility**: Review `_unsupported/` regularly and move files to permanent locations.

### Best Practice

When creating new files:
- Prefer readable formats (.md, .txt, .json) over non-readable (.pdf, .docx)
- This allows Claude to intelligently categorize and assist

