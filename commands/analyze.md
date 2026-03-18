# Analyze Command

In-depth analysis of document repository issues.

## Trigger Words

`analyze`, `diagnose`, `audit`, `analyze documents`

## Precondition

`{TARGET}` path must be determined before execution.

## Supported File Types

Handles all **text-based files**:
- Markup: `.md`, `.markdown`, `.rst`, `.org`
- Plain Text: `.txt`, `.text`
- Documents: `.pdf`, `.docx`, `.xlsx`, `.pptx`, etc.
- Data: `.csv`, `.json`, `.yaml`, `.yml`, `.xml`, `.toml`
- Code: `.py`, `.js`, `.ts`, `.java`, etc.
- Web: `.html`, `.htm`, `.css`, `.scss`
- Config: `.env`, `.ini`, `.cfg`, `.conf`
- Other: `.tex`, `.rtf`, `.log`

## Execution Steps

1. Scan all text-based files in `{TARGET}`
2. Count duplicate filenames by `basename` (ignoring extension)
3. List files scattered in root directory
4. List directory structure inconsistencies
5. List empty directories
6. List files with naming issues
7. Generate analysis report with specific recommendations

## What to Analyze

### Text Files Scan
```bash
# Find all text-based files
find {TARGET} -type f \( \
  -name "*.md" -o -name "*.txt" -o -name "*.rst" -o -name "*.org" \
  -o -name "*.pdf" -o -name "*.docx" -o -name "*.xlsx" -o -name "*.pptx" \
  -o -name "*.csv" -o -name "*.json" -o -name "*.yaml" -o -name "*.yml" \
  -o -name "*.xml" -o -name "*.html" -o -name "*.css" \
  -o -name "*.py" -o -name "*.js" -o -name "*.ts" \
  -o -name "*.log" -o -name "*.tex" \
\) | head -500
```

### Duplicate Files
```bash
# Find duplicate filenames (same basename, different extension)
find {TARGET} -type f \( -name "*.md" -o -name "*.txt" -o -name "*.pdf" ... \) \
  | xargs -I{} basename {} \
  | sort | uniq -c | sort -rn | awk '$1 > 1'
```

### Scattered Files
Files in root directory or outside proper category directories.

### Empty Directories
```bash
# Find empty directories
find {TARGET} -type d -empty ! -path "*/.*"
```

### Directory Structure Issues
- Inconsistent subdirectory patterns across similar directories
- Agent directories with different structures
- Orphan directories (no parent category)

### Naming Issues
- Mixed case styles (camelCase vs kebab-case)
- Mixed languages (CN vs EN)
- Non-descriptive names (新建文件, untitled, etc.)
- Missing extensions

## Output Format

```markdown
# Document Repository Analysis Report
Target: {TARGET}
Date: YYYY-MM-DD

## Statistics
- Total text files: X
- File types: .md (X), .txt (X), .pdf (X), .docx (X), other (X)

## Duplicate Files (X groups)

| Filename | Count | Paths |
|----------|-------|-------|
| report | X | path1, path2 |

## Scattered Files (X files)

| File | Current Location | Recommended Action |
|------|------------------|-------------------|
| file.md | {TARGET}/ | Move to _inbox/pending/ or appropriate category |

## Empty Directories (X dirs)

| Directory |
|-----------|
| path/ |

## Directory Structure Issues

- [Issue 1]
- [Issue 2]

## Naming Issues

- [Issue 1]
- [Issue 2]

## Recommendations

### Immediate Actions (Do Now)
1. [Action]
2. [Action]

### Future Actions (Plan For)
1. [Action]
2. [Action]
```

## Analysis Checklist

- [ ] Scan all text-based files
- [ ] Identify duplicates (same basename, ignore extension)
- [ ] Find scattered files (root or wrong category)
- [ ] Find empty directories
- [ ] Check directory structure consistency
- [ ] Check naming conventions
- [ ] Generate prioritized recommendations
