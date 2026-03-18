# FAQ

Common questions about doctidy skill.

---

## General

### Q: What is doctidy?

**A**: A document repository management skill for Claude that provides:
- Scoring and evaluation of document organization
- Automated cleanup and organization based on content analysis
- File intake and auto-categorization (content-based, not folder-based)
- Status monitoring

Designed for both human users and AI agents working in shared document spaces. Adapts to YOUR folder structure.

---

### Q: How do I use this skill?

**A**: Use the `/doctidy` command:

```bash
/doctidy "status"   # View status
/doctidy "score"     # Score repository
/doctidy "intake"    # Process new files
/doctidy "analyze"   # Analyze issues
/doctidy "organize"  # Clean up
/doctidy "reset"     # Remove doctidy
```

---

### Q: What is `{TARGET}`?

**A**: `{TARGET}` is a placeholder for your document repository root directory. The skill will determine the actual path via:

1. Cached path from previous use
2. User-specified path
3. Current working directory
4. Prompt user if none available

---

### Q: Does doctidy enforce a specific folder structure?

**A**: No. Doctidy is completely folder-agnostic. It:
- Scans YOUR existing folder structure
- Records it in registry.yaml
- Works with whatever organization you have
- Never forces you to use specific folder names

---

## Scoring

### Q: How is the score calculated?

**A**: Score is calculated across 5 dimensions:

| Dimension | Weight |
|-----------|--------|
| Structure Rationality | 25% |
| Naming Consistency | 20% |
| Entropy Control | 25% |
| Index Completeness | 15% |
| Agent Readability | 15% |

See `commands/score.md` for detailed criteria.

---

### Q: What does a good score look like?

**A**: Target score: **75-89 (Good)**

| Score | Status | Action Needed |
|-------|--------|---------------|
| 90-100 | Excellent | Maintain |
| 75-89 | Good | Minor fixes |
| 60-74 | Fair | Some cleanup |
| 40-59 | Poor | Major cleanup |
| <40 | Very Poor | Restructure |

---

## Intake

### Q: How does auto-categorization work?

**A**: Content-based, not folder-based:
1. Reads the file content
2. Extracts keywords and determines file type
3. Finds similar existing files in YOUR structure
4. Suggests the best matching location

If no match found, asks you where to place the file.

---

### Q: What are unsupported files?

**A**: Unsupported files are files that Claude cannot read content from:

| Type | Extensions |
|------|------------|
| PDF | `.pdf` |
| Office (modern) | `.docx`, `.xlsx`, `.pptx` |
| Office (legacy) | `.doc`, `.xls`, `.ppt` |

When intake encounters these files, they are moved to `{TARGET}/_unsupported/`.

---

### Q: What do I do with files in `_unsupported/`?

**A**: You need to manually review and move these files:

1. Check `{TARGET}/_unsupported/`
2. Review each file
3. Move to appropriate permanent location
4. **Important**: Once you move files out of `_unsupported/`, doctidy will not track them again

---

### Q: Why can't doctidy track files after they leave `_unsupported/`?

**A**: Because Claude cannot read the content of these files, it cannot provide intelligent categorization or verify their locations. You are responsible for manually placing them correctly.

**Tip**: To avoid this, prefer creating files in readable formats (.md, .txt, .json) over .pdf or .docx.

---

## Path & Configuration

### Q: How to set target directory?

**A**: Three ways:

1. **Specify in command**: `/doctidy "score /path/to/docs"`
2. **Current directory**: cd to target, then run command
3. **First use**: Skill will prompt for path

---

### Q: How to change cached path?

**A**: Run: `/doctidy "set target to /new/path"`

---

### Q: Can I use this with any directory structure?

**A**: Yes. The skill is folder-agnostic and works with any structure. It scans whatever exists and adapts to your organization.

---

## For AI Agents

### Q: How do AI agents use this?

**A**: Agents should:

1. Read `{TARGET}/README.md` on first access
2. Read `{TARGET}/_index/registry.yaml` to understand YOUR structure
3. Check `{TARGET}/_inbox/pending/` for new files
4. Create output based on content analysis
5. Follow entropy control rules (see `rules.md`)

---

### Q: Agent doesn't update indexes?

**A**: This is by design. Indexes are for quick reference only. If a file isn't found at the indexed location, the agent should scan the directory. No mandatory maintenance required.

---

### Q: Human user doesn't follow conventions?

**A**: The design tolerates this. Features include:
- Index rescanning on init
- Fallback directory scanning
- Forgiving categorization

---

## Troubleshooting

### Q: Skill not triggering?

**A**: Try:
- Explicit trigger: `/doctidy "..."`
- Check skill is installed: `/skills list`
- Check description matches: skill triggers on relevant keywords

---

### Q: Commands not working as expected?

**A**: Check:
1. `{TARGET}` path is correct
2. Run `/doctidy "status"` to diagnose

---

### Q: Accidentally deleted important file?

**A**: Recovery steps:

1. Check `{TARGET}/_trash/`
2. Check git history: `git log --diff-filter=D`
3. Restore from backup if available

---

### Q: How to provide feedback?

**A**: Issues and suggestions welcome:
- File an issue on the skill repository
- Run `analyze` and share results for consultation
