# Reset Command

Remove all doctidy created files and directories from `{TARGET}`.

## Trigger Words

`reset`, `uninstall`, `remove indexes`, `clean slate`

## WARNING

> **This operation is destructive and cannot be undone.**
>
> This command removes ALL doctidy created files:
> - `{TARGET}/_index/` (including registry.yaml and score.md)
> - `{TARGET}/_inbox/` (including all pending files)
> - `{TARGET}/_trash/` (including all trashed files)
> - `{TARGET}/_unsupported/` (including all unsupported files)
> - `{TARGET}/README.md` (if created by doctidy)
>
> **User files are NOT affected.** Only skill-created infrastructure is removed.

## Precondition

`{TARGET}` path must be determined

## What Gets Removed

| Directory/File | Content | Removed? |
|---------------|---------|-----------|
| `_index/` | registry.yaml, score.md | ✅ Yes |
| `_inbox/` | pending files staged for intake | ✅ Yes |
| `_trash/` | deleted files staging area | ✅ Yes |
| `_unsupported/` | unsupported files awaiting manual review | ✅ Yes |
| `README.md` | doctidy entry documentation | ✅ If created by skill |
| **User files** | Your actual documents | ❌ NO |
| **User directories** | knowledge/, projects/, private/, etc. | ❌ NO |

## What Stays

```
{TARGET}/
├── knowledge/        # Your documents - NOT affected
├── projects/         # Your projects - NOT affected
├── private/          # Your private data - NOT affected
└── [other user dirs] # Any user-created content - NOT affected
```

## Execution Flow

### Step 1: List What Will Be Removed

Show user exactly what will be deleted:

```markdown
## Reset Report
Target: {TARGET}

The following doctidy created files will be PERMANENTLY DELETED:

### Directories to Remove
| Directory | Contents |
|-----------|---------|
| _index/ | 2 files (registry.yaml, score.md) |
| _inbox/ | 3 files |
| _trash/ | 5 files |
| _unsupported/ | 2 files |

### Files to Remove
- README.md (if created by doctidy)

### This Will NOT Be Removed
- knowledge/
- projects/
- private/
- All your user files and directories
```

### Step 2: User Confirmation

```
⚠️ WARNING: This operation CANNOT be undone!

Type "yes, reset" to confirm deletion of all doctidy files.
Type "no" to cancel.
```

### Step 3: Execute Deletion (after confirmation)

```bash
# Remove doctidy directories
rm -rf {TARGET}/_index/
rm -rf {TARGET}/_inbox/
rm -rf {TARGET}/_trash/
rm -rf {TARGET}/_unsupported/

# Remove doctidy README if exists
rm -f {TARGET}/README.md
```

### Step 4: Verification

```markdown
## Reset Complete

Removed:
- _index/ ✅
- _inbox/ ✅
- _trash/ ✅
- _unsupported/ ✅
- README.md ✅

Your files in knowledge/, projects/, private/ are intact.
```

## Safety Checks

1. **Confirm target path** - Double-check `{TARGET}` is correct
2. **List what will be removed** - Show user before confirmation
3. **Require explicit confirmation** - User must type "yes, reset"
4. **Verify user files remain** - Confirm knowledge/, projects/, private/ still exist

## FAQ After Reset

### Q: Can I use doctidy again after reset?
**A**: Yes. Run `/doctidy "intake"` to reinitialize and start fresh.

### Q: Will my documents be deleted?
**A**: No. Only doctidy created infrastructure (_index, _inbox, _trash, _unsupported) is removed. Your documents in knowledge/, projects/, private/ are unaffected.

### Q: How do I recover from an accidental reset?
**A**: If using git, you can restore from git history:
```bash
git checkout HEAD -- {TARGET}/_index/ {TARGET}/_inbox/ {TARGET}/_trash/
```

### Q: What's the difference between reset and organize?
**A**:
- **organize**: Cleans up issues, keeps infrastructure
- **reset**: Removes ALL infrastructure, starts fresh

## Use Cases

- **Clean slate**: Want to restructure from scratch
- **Uninstall**: Removing doctidy from a directory
- **Testing**: Want to test fresh initialization
- **Migration**: Moving to a new organization system
