# Init Command

Initialize doctidy infrastructure in `{TARGET}`.

## Trigger Words

`init`, `initialize`, `setup`, `install`

## Precondition

`{TARGET}` path must be determined

## Core Philosophy

**Universal and folder-agnostic.** Doctidy does NOT assume any specific folder structure. It scans whatever exists and builds an index based on YOUR actual organization. Each user's folders will be different.

## What Gets Created

| Directory | Purpose |
|-----------|---------|
| `{TARGET}/_index/` | Index and configuration |
| `{TARGET}/_inbox/pending/` | New file entry point |
| `{TARGET}/_trash/` | Deletion staging area |
| `{TARGET}/_unsupported/` | Files requiring manual processing |

| File | Purpose |
|------|---------|
| `{TARGET}/_index/registry.yaml` | Index of YOUR folder structure |
| `{TARGET}/_unsupported/README.txt` | Instructions for unsupported files |
| `{TARGET}/README.md` | Entry documentation (optional) |

## Directory Structure After Init

```
{TARGET}/                          # Your documents (whatever structure)
├── folder-a/                      # Discovered from scan
├── folder-b/                      # Discovered from scan
├── subfolder/                     # Discovered from scan
├── _index/                       # Doctidy system
│   └── registry.yaml              # YOUR folder structure (scanned)
├── _inbox/
│   └── pending/                   # New files enter here
├── _trash/                       # Deleted files
├── _unsupported/                  # Manual processing needed
└── README.md                     # Entry doc (optional)
```

Note: `_index/`, `_inbox/`, `_trash/`, `_unsupported/` are the ONLY folders doctidy creates. All other folders are YOURS and are discovered by scanning.

## Execution Steps

### Step 1: Check Existing Infrastructure

Check if doctidy infrastructure already exists:

```bash
ls -la {TARGET}/_index/ {TARGET}/_inbox/ {TARGET}/_trash/ {TARGET}/_unsupported/ 2>/dev/null
```

### Step 2: Report Status

```markdown
## Init Report
Target: {TARGET}

### Infrastructure Status
| Directory | Status |
|-----------|--------|
| _index/ | ✅ Exists / ❌ Missing |
| _inbox/ | ✅ Exists / ❌ Missing |
| _trash/ | ✅ Exists / ❌ Missing |
| _unsupported/ | ✅ Exists / ❌ Missing |

### Will Be Created
- _index/registry.yaml (YOUR folder structure)
- _inbox/pending/
- _trash/
- _unsupported/README.txt
- README.md (optional)
```

### Step 3: User Confirmation

```
Initialize doctidy infrastructure in {TARGET}?
- Type 'yes' to proceed
- Type 'no' to cancel
```

### Step 4: Create System Directories

```bash
mkdir -p {TARGET}/_index/
mkdir -p {TARGET}/_inbox/pending/
mkdir -p {TARGET}/_trash/
mkdir -p {TARGET}/_unsupported/
```

### Step 5: Scan YOUR Folder Structure

This is the key step - discover whatever folder structure EXISTS:

```bash
# Recursively scan all directories EXCEPT doctidy system folders
find {TARGET} -type d \
  ! -path "*/_index/*" \
  ! -path "*/_inbox/*" \
  ! -path "*/_trash/*" \
  ! -path "*/_unsupported/*" \
  ! -path "*/.git/*" \
  ! -path "*/.DS_Store" \
  ! -path "*/node_modules/*" \
  ! -path "*/__pycache__/*" \
  -maxdepth 5
```

### Step 6: Build Registry from Scan

Create `registry.yaml` with YOUR actual folder structure:

```yaml
# doctidy directory registry
# This file records YOUR folder structure (discovered by scan)
version: "1.0"
created: YYYY-MM-DD
lastUpdated: YYYY-MM-DD

# Your directories (discovered by scan, YOUR structure)
# This adapts to whatever folders YOU have
directories:
  root:
    - {first-level-folder}
    - {second-level-folder}
  nested:
    - {parent}/{child}
    - {parent}/{child}/{grandchild}

# File type distribution (scanned)
fileTypes:
  markup: [count]
  code: [count]
  data: [count]
  config: [count]
  other: [count]

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

These files could not be automatically processed because Claude cannot read their content.

Please manually:
1. Review each file
2. Move to appropriate directory
3. Once moved out, these files will no longer be tracked

Supported types here:
- .pdf (binary document)
- .docx, .xlsx, .pptx (modern Office)
- .doc, .xls, .ppt (legacy Office)

Tip: To avoid this, prefer creating files in readable formats (.md, .txt, .json)
```

Create `README.md` (optional - adapt to YOUR structure):
```markdown
# {TARGET}

Managed by doctidy.

## System Directories (Doctidy)

| Directory | Purpose |
|-----------|---------|
| _index/ | Index configuration |
| _inbox/pending/ | New files for intake |
| _trash/ | Deleted files staging |
| _unsupported/ | Manual processing required |

## Your Directories (Discovered)

The following directories were found in your repository:
{discovered_list}

## Commands

/doctidy "status"   # View status
/doctidy "score"     # Score repository
/doctidy "intake"    # Process new files
/doctidy "analyze"   # Analyze issues
/doctidy "organize"  # Clean up
/doctidy "reset"     # Remove doctidy
```

### Step 8: Verification

```markdown
## Init Complete

Created:
- _index/ ✅
- _inbox/pending/ ✅
- _trash/ ✅
- _unsupported/README.txt ✅
- README.md ✅
- _index/registry.yaml ✅ (YOUR folder structure)

Your folders discovered: X directories
Your files discovered: X files

Doctidy is ready! Run `/doctidy "status"` to verify.
```

## Registry Example

After init, your registry.yaml might look like:

```yaml
version: "1.0"
created: "2026-03-18"
lastUpdated: "2026-03-18"

directories:
  root:
    - documents
    - archive
    - work
  nested:
    - documents/notes
    - documents/projects
    - work/reports
    - archive/2024

fileTypes:
  markup: 45
  code: 23
  data: 12
  config: 8

rules:
  newFileLocation: "_inbox/pending/"
  deleteLocation: "_trash/"
  scanOnAccess: true
```

## Safety

- **Does NOT delete existing files** - Only creates system folders
- **Does NOT assume folder names** - Scans whatever exists
- **Does NOT enforce structure** - Adapts to YOUR organization
- **Only creates**: `_index/`, `_inbox/`, `_trash/`, `_unsupported/`
- **Safe to run multiple times** - Idempotent, rescans each run
