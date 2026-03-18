# Entropy Control Rules

Core rules to prevent document repository entropy and maintain order.

---

## Core Philosophy

**Content-based organization, not folder-based rules.** Doctidy analyzes WHAT your files ARE (content, type, topic) rather than enforcing predefined folder structures. It works with YOUR organization.

---

## File Readability

### Claude Can Read (Content Analysis Available)

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

**Why**: Centralized entry point makes categorization easier.

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

**Rule**: No text files in root directory outside proper categories.

**Allowed Locations**:
- `{TARGET}/_inbox/pending/` (staging)
- `{TARGET}/_trash/` (deleted staging)
- `{TARGET}/_unsupported/` (manual processing - temporary)
- YOUR category directories (whatever structure you have)

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

**Duplicate Definition**: Files with same basename AND same readable type. `report.md` and `report.pdf` are NOT duplicates (different types).

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

### Universal Structure

Doctidy does NOT enforce any folder structure. Your structure is discovered by scanning:

```
{TARGET}/
├── [your-folder-1]/           # Discovered by scan
├── [your-folder-2]/           # Discovered by scan
├── [subfolder/]/              # Discovered by scan
├── _index/                    # Doctidy system
│   ├── registry.yaml          # YOUR structure recorded
│   └── score.md
├── _inbox/                    # Doctidy system
│   └── pending/
├── _trash/                    # Doctidy system
├── _unsupported/              # Doctidy system
└── README.md                  # Optional
```

### Naming Conventions

| Type | Convention | Example |
|------|------------|---------|
| Directories | Whatever you use | `work/`, `notes/`, `archive/` |
| Files | Consistent pattern | `report-2026-03.md`, `notes-daily.md` |
| Dates | ISO 8601 | `YYYY-MM-DD` |

### Language

Your documents can be in any language. Doctidy analyzes content regardless of language.

---

## For AI Agents

### Agent Workflow

```
1. Read {TARGET}/README.md (if exists)
2. Read {TARGET}/_index/registry.yaml (YOUR structure)
3. Check _inbox/pending/ for new files
4. Do work
5. Save output to appropriate location (based on content)
6. If unsure → _inbox/pending/
7. If output is non-readable (.pdf, .docx, etc.) → _unsupported/
```

### Agent Responsibilities

- Create files in proper locations based on content
- Use readable formats when possible (.md over .docx)
- Put non-readable outputs to `{TARGET}/_unsupported/`
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

1. Run `/doctidy "init"` to rescan and rebuild index
2. Registry.yaml will be regenerated from your current structure

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
