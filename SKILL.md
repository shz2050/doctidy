---
name: doctidy
description: Document tidying and organization tool. Triggered when users mention document organization, scoring, cleanup, entropy control, auto-categorization, file intake, or managing a document directory. Helps maintain document order, provide organization scores, perform cleanup, and auto-categorize new files.
---

# Doctidy

A document tidying skill that brings order to chaos - providing scoring, analysis, organization, and intake commands.

> **For AI Agents & Humans**: Helps manage document directories by providing scoring, analysis, cleanup, and auto-categorization. Designed for both AI Agents and human users working in shared document spaces.

## Quick Start

| Command | Trigger Words | Description |
|---------|--------------|-------------|
| `/skill doctidy "init"` | init, initialize, setup, install | Initialize doctidy infrastructure |
| `/skill doctidy "score"` | score, scoring, rate, health check | Score the document repository |
| `/skill doctidy "analyze"` | analyze, diagnose, audit | Deep analysis of issues |
| `/skill doctidy "organize"` | organize, cleanup, clean up | Execute cleanup operations |
| `/skill doctidy "intake"` | intake, categorize, sort files | Auto-categorize new files |
| `/skill doctidy "status"` | status, quick status | Quick status view |
| `/skill doctidy "reset"` | reset, uninstall, remove indexes | Remove all doctidy created files |

---

## Path Resolution

`{TARGET}` = the document repository root directory

Path is determined by (in order of priority):
1. Cached config from previous use
2. User-specified path
3. Current working directory
4. Prompt user if none available

---

## Directory Structure

```
{TARGET}/
├── _index/
│   ├── registry.yaml    # Directory index configuration
│   └── score.md         # Latest scoring report
├── _inbox/
│   └── pending/        # New file entry point
├── _trash/             # Deletion staging area
├── _unsupported/       # Files requiring manual processing
│   └── README.txt      # Instructions for manual review
└── README.md           # Entry documentation
```

---

## Supported File Types

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

### Claude Cannot Read (Manual Required)

| Category | Extensions | Destination |
|----------|------------|-------------|
| PDF | `.pdf` | `_unsupported/` |
| Office (modern) | `.docx`, `.xlsx`, `.pptx` | `_unsupported/` |
| Office (legacy) | `.doc`, `.xls`, `.ppt` | `_unsupported/` |

### Ignored Files (Keep Original Location)

These files are **completely ignored** - no tracking, no categorization, keep as-is:

| Category | Extensions |
|----------|------------|
| Images | `.png`, `.jpg`, `.jpeg`, `.gif`, `.bmp`, `.svg`, `.webp`, `.ico` |
| Video | `.mp4`, `.mov`, `.avi`, `.mkv`, `.webm` |
| Audio | `.mp3`, `.wav`, `.flac`, `.aac`, `.ogg` |
| Archives | `.zip`, `.tar`, `.gz`, `.rar`, `.7z` |
| Other | `.exe`, `.dmg`, `.app` |

> **Images, videos, audio, and archives are never moved or tracked.**

---

## Commands Overview

### 1. Init
Initialize doctidy infrastructure in a directory.
- **Triggers**: init, initialize, setup, install
- **Output**: Creates _index/, _inbox/, _trash/, _unsupported/ etc.
- **Load**: `commands/init.md` for initialization procedures

### 2. Score
Evaluate and score the document repository.
- **Triggers**: score, scoring, rate, grade, current status, health check
- **Output**: Scoring report with dimension scores and recommendations
- **Load**: `commands/score.md` for detailed scoring criteria

### 3. Analyze
In-depth analysis of document repository issues.
- **Triggers**: analyze, diagnose, audit
- **Output**: Analysis report with specific recommendations
- **Load**: `commands/analyze.md` for detailed analysis rules

### 4. Organize
Execute automatic cleanup operations.
- **Triggers**: organize, cleanup, clean up, fix
- **Recommended**: Run `analyze` first to understand issues
- **Load**: `commands/organize.md` for detailed operations

### 5. Intake
Process new file entry, auto-categorize or ask for direction.
- **Triggers**: intake, categorize, file intake, process new files, sort files
- **Output**: Categorization suggestions and move operations
- **Load**: `commands/intake.md` for categorization rules

### 6. Status
Quick view of document repository status.
- **Triggers**: status, quick status, directory status
- **Output**: Quick status report with stats and checks
- **Load**: `commands/status.md` for status format

### 7. Reset
Remove all doctidy created files and directories.
- **Triggers**: reset, uninstall, remove indexes
- **Warning**: This removes all skill-created files
- **Load**: `commands/reset.md` for reset procedures

---

## Scoring Dimensions

| Dimension | Weight | Description |
|-----------|--------|-------------|
| Structure Rationality | 25% | Directory hierarchy, category clarity |
| Naming Consistency | 20% | File naming standards |
| Entropy Control | 25% | Duplicates, scattered files, invalid files |
| Index Completeness | 15% | registry.yaml, entry docs |
| Agent Readability | 15% | README, protocol clarity |

## Grading Scale

| Score | Status |
|-------|--------|
| 90-100 | Excellent |
| 75-89 | Good |
| 60-74 | Fair |
| 40-59 | Needs Work |
| <40 | Poor |

---

## Reference Files

| File | Purpose | Load When |
|------|--------|-----------|
| `commands/init.md` | Initialization procedures | Init command |
| `commands/score.md` | Scoring criteria | Score command |
| `commands/analyze.md` | Analysis rules | Analyze command |
| `commands/organize.md` | Cleanup operations | Organize command |
| `commands/intake.md` | Categorization rules | Intake command |
| `commands/status.md` | Status format | Status command |
| `commands/reset.md` | Reset procedures | Reset command |
| `rules.md` | Entropy control rules | Always available |
| `faq.md` | Common questions | As needed |

---

## Core Rules

| Rule | Description |
|------|-------------|
| Inbox Entry | New files → `{TARGET}/_inbox/pending/` |
| Trash Before Delete | Move to `{TARGET}/_trash/` before deleting |
| No Scattering | No md files outside proper categories |
| Path Caching | Target path cached to avoid repeated prompts |

---

## Open Source

- **Path-agnostic**: No hardcoded paths
- **Configurable**: Keywords and rules can be customized
- **Extensible**: Easy to add new categorization rules
- **MIT License**: Free for personal and commercial use

---

*For detailed implementation, see reference files listed above.*
