# Init Command

Initialize doctidy infrastructure and build content index for `{TARGET}`.

## Trigger Words

`init`, `initialize`, `setup`, `install`

## Precondition

`{TARGET}` path must be determined

## Core Philosophy

**Agent-first indexing.** Build a content index that lets AI agents find documents by topic, summary, and keywords - not just folder structure.

## What Gets Created

| Directory | Purpose |
|-----------|---------|
| `{TARGET}/_index/` | Index and configuration |
| `{TARGET}/_inbox/pending/` | New file entry point |
| `{TARGET}/_trash/` | Deletion staging area |
| `{TARGET}/_unsupported/` | Files requiring manual processing |

| File | Purpose |
|------|---------|
| `{TARGET}/_index/registry.yaml` | **Content index** with summaries and keywords |
| `{TARGET}/_unsupported/README.txt` | Instructions for unsupported files |

## Directory Structure After Init

```
{TARGET}/
├── [your-folders/]                   # Your documents
├── _index/                          # Doctidy system
│   └── registry.yaml                 # Content index (agent's search guide)
├── _inbox/
│   └── pending/                     # New files for intake
├── _trash/                         # Deleted files
└── _unsupported/                    # Manual processing needed
```

## Execution Steps

### Step 1: Check Infrastructure

```bash
ls -la {TARGET}/_index/ {TARGET}/_inbox/ {TARGET}/_trash/ {TARGET}/_unsupported/ 2>/dev/null
```

### Step 2: User Confirmation

```markdown
## Init Report
Target: {TARGET}

This will:
1. Create system directories (_index, _inbox, _trash, _unsupported)
2. Scan all readable files
3. Build content index with summaries and keywords

Proceed? (yes/no)
```

### Step 3: Create System Directories

```bash
mkdir -p {TARGET}/_index/
mkdir -p {TARGET}/_inbox/pending/
mkdir -p {TARGET}/_trash/
mkdir -p {TARGET}/_unsupported/
```

### Step 4: Scan Files

Scan all readable files (exclude system folders):

```bash
find {TARGET} -type f \
  ! -path "*/_index/*" \
  ! -path "*/_inbox/*" \
  ! -path "*/_trash/*" \
  ! -path "*/_unsupported/*" \
  ! -path "*/.git/*" \
  ! -path "*/.DS_Store" \
  ! -path "*/node_modules/*" \
  ! -path "*/__pycache__/*" \
  -name "*.md" -o -name "*.txt" -o -name "*.json" -o -name "*.yaml" \
  -o -name "*.yml" -o -name "*.csv" -o -name "*.toml"
```

### Step 5: Generate Content Index

For each readable file, generate:

```yaml
files:
  - path: "relative/path/to/file.md"
    type: "markdown"
    summary: "1-2 sentence description of content"
    keywords: ["keyword1", "keyword2", "keyword3"]
    tags: ["project", "type"]  # auto-detected or user-defined
    lastModified: "YYYY-MM-DD"
```

### Step 6: Build registry.yaml

Create the content index:

```yaml
# doctidy content index
# This index lets AI agents find documents by topic, not just path
version: "2.0"
created: YYYY-MM-DD
lastUpdated: YYYY-MM-DD

# Index statistics
stats:
  totalFiles: X
  byType:
    markdown: X
    json: X
    yaml: X
    other: X

# Content index - the agent's search guide
files:
  - path: "knowledge/progress/projects/flowstage.md"
    type: markdown
    summary: "Flowstage iOS app iteration progress and ASO optimization tasks"
    keywords: [flowstage, ios, aso, testflight, 进度]
    tags: [project, progress]
    lastModified: "2026-03-16"

  - path: "knowledge/agents/flowstage/flowstage-understanding.md"
    type: markdown
    summary: "Deep product understanding of Flowstage - a flow platform for work-life integration"
    keywords: [flowstage, product, flow, focus, ritual]
    tags: [product, understanding]
    lastModified: "2026-03-16"

# Rules
rules:
  newFileLocation: "_inbox/pending/"
  deleteLocation: "_trash/"
  scanOnAccess: true
```

### Step 7: Create System Files

Create `_unsupported/README.txt`:
```
=== Unsupported Files ===

Files that cannot be auto-indexed (binary/non-readable).

Please manually move these to permanent locations.

Binary types: .pdf, .docx, .xlsx, .pptx, .doc, .xls, .ppt
Images: .png, .jpg, .gif, etc.
Archives: .zip, .tar, .gz, etc.
```

### Step 8: Verification

```markdown
## Init Complete

Created:
- _index/ ✅
- _inbox/pending/ ✅
- _trash/ ✅
- _unsupported/README.txt ✅
- _index/registry.yaml ✅ (X files indexed)

Run `/doctidy "search <keyword>"` to find documents.
```

## Content Index Format

The registry.yaml is now a **content-first index**:

```yaml
files:
  - path: "relative/path.md"
    type: markdown|json|yaml|text|code|config
    summary: "What this document is about"
    keywords: [extracted, keywords, for, search]
    tags: [optional, tags, for, filtering]
    lastModified: "YYYY-MM-DD"
```

### Summary Generation

For each file, generate a 1-2 sentence summary:
- Read first 10 lines of the file
- Extract main topic/theme
- Focus on WHAT the file is about, not implementation details

### Keyword Extraction

Extract 5-10 keywords from:
- Filename (already informative)
- First paragraph/topic sentences
- Section headers
- Repeated important terms

## Agent Usage

With this index, an agent can:

```bash
# Search by keyword
/doctidy "search flowstage"

/doctidy "search product understanding"

/doctidy "search ASO optimization"
```

The index returns matching files with summaries, so the agent can quickly find relevant documents without reading every file.

## Safety

- **Does NOT modify existing files** - Only creates system folders
- **Does NOT delete content** - Only builds read-only index
- **Incremental updates** - New files indexed via intake
- **Safe to rerun** - Rescans and rebuilds index each time
