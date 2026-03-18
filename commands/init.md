# Init Command

Initialize doctidy infrastructure in `{TARGET}`.

## Trigger Words

`init`, `initialize`, `setup`, `install`

## Precondition

`{TARGET}` path must be determined

## What Gets Created

| Directory | Purpose |
|-----------|---------|
| `{TARGET}/_index/` | Directory index configuration |
| `{TARGET}/_inbox/pending/` | New file entry point |
| `{TARGET}/_trash/` | Deletion staging area |
| `{TARGET}/_unsupported/` | Files requiring manual processing |

| File | Purpose |
|------|---------|
| `{TARGET}/_index/registry.yaml` | Directory index configuration |
| `{TARGET}/_unsupported/README.txt` | Instructions for unsupported files |
| `{TARGET}/README.md` | Entry documentation |

## Directory Structure After Init

```
{TARGET}/
├── _index/
│   └── registry.yaml
├── _inbox/
│   └── pending/
├── _trash/
├── _unsupported/
│   └── README.txt
└── README.md
```

## Execution Steps

### Step 1: Check Existing Structure

Check if any infrastructure already exists:

```bash
ls -la {TARGET}/_index/ {TARGET}/_inbox/ {TARGET}/_trash/ {TARGET}/_unsupported/ 2>/dev/null
```

### Step 2: Report Status

```markdown
## Init Report
Target: {TARGET}

### Existing Structure
| Directory | Status |
|-----------|--------|
| _index/ | ✅ Exists / ❌ Missing |
| _inbox/ | ✅ Exists / ❌ Missing |
| _trash/ | ✅ Exists / ❌ Missing |
| _unsupported/ | ✅ Exists / ❌ Missing |

### Will Be Created
- _index/registry.yaml
- _inbox/pending/
- _trash/
- _unsupported/README.txt
- README.md
```

### Step 3: User Confirmation

```
Initialize doctidy infrastructure in {TARGET}?
- Type 'yes' to proceed
- Type 'no' to cancel
```

### Step 4: Create Directories

```bash
mkdir -p {TARGET}/_index/
mkdir -p {TARGET}/_inbox/pending/
mkdir -p {TARGET}/_trash/
mkdir -p {TARGET}/_unsupported/
```

### Step 5: Create Files

Create `registry.yaml`:
```yaml
# doctidy directory registry
version: "1.0"
created: YYYY-MM-DD
lastUpdated: YYYY-MM-DD
```

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

Create `README.md`:
```markdown
# {TARGET}

Managed by doctidy.

## Directories

| Directory | Purpose |
|-----------|---------|
| _index/ | Index configuration |
| _inbox/pending/ | New files for intake |
| _trash/ | Deleted files staging |
| _unsupported/ | Manual processing required |
| knowledge/ | Your knowledge base |
| projects/ | Your projects |
| private/ | Private data |

## Commands

```bash
/skill doctidy "status"   # View status
/skill doctidy "score"     # Score repository
/skill doctidy "intake"    # Process new files
/skill doctidy "analyze"   # Analyze issues
/skill doctidy "organize"  # Clean up
/skill doctidy "reset"      # Remove doctidy
```
```

Create `registry.yaml`:
```yaml
# doctidy directory registry
version: "1.0"
created: YYYY-MM-DD
lastUpdated: YYYY-MM-DD
paths:
  inbox: _inbox/pending/
  trash: _trash/
  unsupported: _unsupported/
  knowledge: knowledge/
  projects: projects/
  private: private/
```

### Step 6: Verification

```markdown
## Init Complete

Created:
- _index/ ✅
- _inbox/pending/ ✅
- _trash/ ✅
- _unsupported/README.txt ✅
- README.md ✅
- _index/registry.yaml ✅

Doctidy is ready! Run `/skill doctidy "status"` to verify.
```

## Safety

- Does NOT delete existing files
- Does NOT modify existing structure
- Only creates missing directories and files
- Safe to run multiple times (idempotent)
