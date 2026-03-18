# Status Command

Quick view of document repository status.

## Trigger Words

`status`, `quick status`, `directory status`

## Precondition

`{TARGET}` path must be determined

## What to Check

### Basic Stats
```bash
# File count
find {TARGET} -name "*.md" -type f ! -path "*/.*" | wc -l

# Directory count
find {TARGET} -type d ! -path "*/.*" ! -path "*/.git/*" | wc -l

# Agent directories (if exists)
ls {TARGET}/knowledge/agents/ 2>/dev/null | wc -l
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
find {TARGET} -name "*.md" -type f | xargs -I{} basename {} | sort | uniq -c | sort -rn | awk '$1 > 1'

# Empty directories
find {TARGET} -type d -empty ! -path "*/.*" | wc -l

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
| Agent directories | X |

## Latest Score
| Item | Value |
|------|-------|
| Overall Score | XX/100 |
| Status | [STATUS] |
| Last Scored | YYYY-MM-DD |

## Quick Checks
| Item | Status |
|------|--------|
| registry.yaml | ✅/❌ |
| README.md | ✅/❌ |
| _index/score.md | ✅/❌ |
| Duplicate files | X groups |
| Empty directories | X |
| Pending in inbox | X |

## Directory Structure
```
{TARGET}/
├── _index/       | ✅/❌
├── _inbox/       | ✅/❌
├── _trash/       | ✅/❌
├── knowledge/    | ✅/❌
├── projects/     | ✅/❌ (if exists)
└── private/      | ✅/❌ (if exists)
```

## Recent Issues (Last Score)
- [Issue 1]
- [Issue 2]

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

## Quick Health判断

Based on stats and checks:

| Condition | Health |
|-----------|--------|
| Score >= 75, No duplicates, No empty dirs | 🟢 Healthy |
| Score 60-74, Few issues | 🟡 Fair |
| Score < 60, Multiple issues | 🔴 Needs cleanup |
