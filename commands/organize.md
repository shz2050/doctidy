# Organize Command

Execute automatic cleanup operations.

## Trigger Words

`organize`, `cleanup`, `clean up`, `fix`

## Precondition

1. `{TARGET}` path must be determined
2. Running `analyze` first is **recommended** to understand issues, but not required

## Execution Steps

### Step 1: Dry-Run (Show Planned Operations)

Before executing any changes, display what will be done:

```markdown
## Planned Operations

### Files to Move
1. `file.md` → `{TARGET}/_inbox/pending/`
2. `old.md` → `{TARGET}/_trash/` (deletion staging)

### Files to Delete (Duplicates)
1. `duplicate.md` at {path} (keep at {path})

### Directories to Clean
1. `empty-dir/` - will be removed

### Files to Rename
1. `{TARGET}/新建文件.md` → descriptive-name.md
```

### Step 2: User Confirmation

Ask user to confirm before executing:

```
Execute the above operations? (yes/no)
- Type 'yes' to proceed
- Type 'no' to cancel
- Type 'select' to choose specific operations
```

### Step 3: Execute Operations

After confirmation, execute in order:
1. Move files to _inbox/pending/
2. Move duplicate files to _trash/
3. Clean empty directories
4. Rename files if needed

### Step 4: Update Score

After cleanup, update `_index/score.md` with new score.

## Duplicate File Strategy

When duplicates found, use this priority:

1. **User Explicit**: User specifies which to keep
2. **Business Relevance**: Keep directory with business value
3. **Recency**: Keep most recently modified
4. **Size**: Keep larger file

## Operations List

| Operation | Command | Risk Level |
|-----------|---------|------------|
| Move to inbox | `mv file {TARGET}/_inbox/pending/` | Low (reversible) |
| Move to trash | `mv file {TARGET}/_trash/` | Medium (reversible) |
| Delete permanently | `rm file` | High (irreversible) |
| Rename | `mv old new` | Low (reversible) |
| Remove empty dir | `rmdir dir` | Low |

## Safety Rules

1. **Always confirm** before destructive operations
2. **Trash before delete** - never rm unless in _trash/ for 30+ days
3. **Backup plan** - git commit before major changes
4. **Dry-run first** - show planned operations before executing

## Output Format

```markdown
# Organize Report
Target: {TARGET}
Date: YYYY-MM-DD

## Operations Executed

| Operation | Status |
|-----------|--------|
| Move X files to inbox | ✅ |
| Move X files to trash | ✅ |
| Delete X duplicates | ✅ |
| Clean X empty dirs | ✅ |

## Results

- Files moved: X
- Files deleted: X
- Directories cleaned: X
- New score: XX/100

## Post-Cleanup Status
[Updated status summary]
```
