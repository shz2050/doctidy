# Score Command

Evaluate and score the document repository.

## Trigger Words

`score`, `scoring`, `rate`, `grade`, `current status`, `health check`

## Precondition

`{TARGET}` path must be determined before execution.

## Supported File Types

This skill handles all **text-based files**:

| Category | Extensions |
|----------|------------|
| Markup | `.md`, `.markdown`, `.rst`, `.org` |
| Plain Text | `.txt`, `.text` |
| Documents | `.pdf`, `.docx`, `.doc`, `.xlsx`, `.xls`, `.pptx`, `.ppt` |
| Data | `.csv`, `.tsv`, `.json`, `.yaml`, `.yml`, `.xml`, `.toml` |
| Code | `.py`, `.js`, `.ts`, `.java`, `.c`, `.cpp`, `.h`, `.go`, `.rs`, `.rb`, `.php`, etc. |
| Web | `.html`, `.htm`, `.css`, `.scss`, `.sass`, `.less` |
| Config | `.env`, `.ini`, `.cfg`, `.conf`, `.properties` |
| Other | `.tex`, `.rtf`, `.log` |

> **Note**: Binary files (images, videos, audio, archives) are ignored by default but can be tracked via registry.

## Execution Steps

1. Scan `{TARGET}` directory structure
2. Scan all supported text-based files
3. Check for duplicate files (same `basename` regardless of extension)
4. Check for scattered files (outside proper categories)
5. Check for empty directories
6. Check for invalid/poorly named files
7. Check index completeness (registry.yaml, README.md)
8. Calculate dimension scores and generate report

## Score Calculation

```python
# Pseudocode
overall_score = (
    structure_score * 0.25 +
    naming_score * 0.20 +
    entropy_score * 0.25 +
    index_score * 0.15 +
    agent_score * 0.15
)
```

## Output Format

```markdown
# Document Repository Scoring Report
Date: YYYY-MM-DD
Target: {TARGET}

## Overall Score: XX/100 [STATUS]

## Dimension Scores
| Dimension | Score | Weight | Weighted |
|-----------|-------|--------|----------|
| Structure Rationality | XX/100 | 25% | XX.XX |
| Naming Consistency | XX/100 | 20% | XX.XX |
| Entropy Control | XX/100 | 25% | XX.XX |
| Index Completeness | XX/100 | 15% | XX.XX |
| Agent Readability | XX/100 | 15% | XX.XX |

## Issues Found
### 🔴 High Priority
- [Issue 1]
- [Issue 2]

### 🟡 Medium Priority
- [Issue 3]

### 🟢 Low Priority
- [Issue 4]

## Recommended Actions
1. [Action 1]
2. [Action 2]
3. [Action 3]
```

## Grading Scale

| Score | Status | Action |
|-------|--------|--------|
| 90-100 | Excellent | Maintain current state |
| 75-89 | Good | Minor improvements |
| 60-74 | Fair | Some cleanup needed |
| 40-59 | Needs Work | Significant reorganization needed |
| <40 | Poor | Major restructure recommended |

## Scoring Criteria Details

### Structure Rationality (25%)
- Depth: 3-4 levels optimal, >5 levels penalty
- Category clarity: Clear purpose for each directory
- Logical grouping: Related files together

### Naming Consistency (20%)
- Case style consistency
- Language consistency (all CN or all EN, or clear separation)
- Pattern consistency (e.g., date format: YYYY-MM-DD)

### Entropy Control (25%)
- 0 duplicates = 100, each duplicate -10
- 0 scattered = 100, each scattered file -5
- 0 empty dirs = 100, each empty dir -2

### Index Completeness (15%)
- registry.yaml exists: 50 points
- README.md exists: 30 points
- _index/ structure: 20 points

### Agent Readability (15%)
- README clarity and completeness
- Protocol documented
- Entry points clear
