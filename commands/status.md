# Status Command

Quick view of document repository status.

## Trigger Words

`status`, `quick status`, `directory status`

## Precondition

`{TARGET}` path must be determined

## Core Philosophy

**Folder-agnostic.** Status shows YOUR actual folder structure, not a predefined one.

## What to Check

### Basic Stats
```bash
# File count by type
find {TARGET} -type f \
  ! -path "*/_index/*" \
  ! -path "*/_inbox/*" \
  ! -path "*/_trash/*" \
  ! -path "*/_unsupported/*" \
  ! -path "*/.git/*" \
  -name "*.md" | wc -l

# Directory count (excluding system folders)
find {TARGET} -type d \
  ! -path "*/_index/*" \
  ! -path "*/_inbox/*" \
  ! -path "*/_trash/*" \
  ! -path "*/_unsupported/*" \
  ! -path "*/.git/*" \
  ! -path "*/.*" | wc -l
```

### Key Files Check
| File | Expected Location | Status |
|------|------------------|--------|
| registry.yaml | `{TARGET}/_index/registry.yaml` | ✅/❌ |
| README.md | `{TARGET}/README.md` | ✅/❌ |
| score.md | `{TARGET}/_index/score.md` | ✅/❌ |

### Issue Counts
```bash
# Duplicate groups
find {TARGET} -type f \
  ! -path "*/_index/*" \
  ! -path "*/_inbox/*" \
  ! -path "*/_trash/*" \
  ! -path "*/_unsupported/*" \
  -name "*.md" | xargs -I{} basename {} | sort | uniq -c | sort -rn | awk '$1 > 1'

# Empty directories (excluding system)
find {TARGET} -type d -empty \
  ! -path "*/_index/*" \
  ! -path "*/_inbox/*" \
  ! -path "*/_trash/*" \
  ! -path "*/_unsupported/*" \
  ! -path "*/.git/*" \
  ! -path "*/.*" | wc -l

# Inbox pending
ls {TARGET}/_inbox/pending/ 2>/dev/null | wc -l
```

### Latest Score
Read from `{TARGET}/_index/score.md` if exists.

## Output Format

```markdown
# Document Repository Status
Target: {TARGET}
Date: YYYY-MM-DD

## Basic Stats
| Metric | Value |
|--------|-------|
| Total files (.md) | X |
| Total directories | X |
| Total file types | X |

## System Infrastructure
| Directory | Status |
|-----------|--------|
| _index/ | ✅/❌ |
| _inbox/ | ✅/❌ (X pending) |
| _trash/ | ✅/❌ (X files) |
| _unsupported/ | ✅/❌ |

## Latest Score
| Item | Value |
|------|-------|
| Overall Score | XX/100 |
| Status | [STATUS] |
| Last Scored | YYYY-MM-DD |

## Your Folder Structure (Discovered)
| Directory | Files |
|-----------|-------|
| folder-a/ | X |
| folder-b/ | X |
| folder-c/subfolder/ | X |

## Quick Checks
| Item | Status |
|------|--------|
| registry.yaml | ✅/❌ |
| README.md | ✅/❌ |
| _index/score.md | ✅/❌ |
| Duplicate files | X groups |
| Empty directories | X |
| Pending in inbox | X |

## Next Recommended Action
[Based on current state and last score]
```

## Status Indicators

| Symbol | Meaning |
|--------|---------|
| ✅ | Exists / Good |
| ⚠️ | Warning / Needs attention |
| ❌ | Missing / Problem |
| — | Not applicable / Skipped |

## Health判断

Based on stats and checks:

| Condition | Health |
|-----------|--------|
| Score >= 75, No duplicates, No empty dirs | 🟢 Healthy |
| Score 60-74, Few issues | 🟡 Fair |
| Score < 60, Multiple issues | 🔴 Needs cleanup |
