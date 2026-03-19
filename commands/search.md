# Search Command

Search documents by keyword, topic, or content summary.

## Trigger Words

`search`, `find`, `query`, `lookup`, `find docs`

## Precondition

`{TARGET}` path must be determined

## Core Philosophy

**Content-first search.** Search the content index (not just filenames) to find documents by topic, summary, and keywords.

## Execution Flow

### Step 1: Parse Query

Extract search terms from user query:
- Remove common words ("the", "a", "find", "search")
- Keep significant keywords
- Support Chinese and English

### Step 2: Read Content Index

Load `{TARGET}/_index/registry.yaml`

### Step 3: Match Documents

For each file in index:
1. Check path keywords
2. Check summary
3. Check keywords array
4. Check tags

### Step 4: Rank Results

Score by match quality:
- Exact keyword in summary: +10
- Keyword in keywords array: +5
- Keyword in path: +3
- Multiple matches: bonus

### Step 5: Output Results

```markdown
# Search Results: "flowstage"
Target: {TARGET}
Query: flowstage

## Found 2 documents

### 1. knowledge/progress/projects/flowstage.md
**Summary:** Flowstage iOS app iteration progress and ASO optimization tasks
**Keywords:** [flowstage, ios, aso, testflight, 进度]
**Tags:** [project, progress]
**Last Modified:** 2026-03-16
**Match:** keyword + path

### 2. knowledge/agents/flowstage/flowstage-understanding.md
**Summary:** Deep product understanding of Flowstage - a flow platform for work-life integration
**Keywords:** [flowstage, product, flow, focus, ritual]
**Tags:** [product, understanding]
**Last Modified:** 2026-03-16
**Match:** keyword + summary

---

## Usage Tips

/doctidy "search <keyword>"     # Search by keyword
/doctidy "search <topic>"       # Search by topic
/doctidy "search project"       # Find all project docs
/doctidy "search daily notes"   # Find daily notes
```

## Search Examples

| Query | Results |
|-------|---------|
| `flowstage` | flowstage product docs, progress docs |
| `product` | all product-related documents |
| `meeting` | meeting notes, minutes |
| `daily` | daily logs, daily reports |
| `TODO` | todo lists, task files |

## Output Format

### Success (found results)
```markdown
# Search Results: "{query}"
Target: {TARGET}

## Found {N} documents

### {rank}. {path}
**Summary:** {summary}
**Keywords:** {keywords}
**Tags:** {tags}
**Last Modified:** {date}
**Match:** {match-type}
```

### No Results
```markdown
# Search Results: "{query}"
Target: {TARGET}

No documents found matching "{query}".

Suggestions:
- Try simpler keywords
- Check spelling
- Use broader terms

Run `/doctidy "analyze"` to rebuild the index.
```

## Index-Based Search

This command searches the pre-built content index. For best results:
1. Run `/doctidy "init"` or `/doctidy "intake"` to build/update index
2. Keep summaries accurate and keywords up-to-date

## Fallback

If no index exists, search by filename:

```bash
find {TARGET} -type f -name "*{query}*" ! -path "*/_*"
```
