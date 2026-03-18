# FAQ

Common questions about doctidy skill.

---

## General

### Q: What is doctidy?

**A**: A document repository management skill for Claude that provides:
- Scoring and evaluation of document organization
- Automated cleanup and organization
- File intake and auto-categorization
- Status monitoring

Designed for both human users and AI agents working in shared document spaces.

---

### Q: How do I use this skill?

**A**: Use the `/skill` command:

```bash
/skill doctidy "score"
/skill doctidy "analyze"
/skill doctidy "organize"
/skill doctidy "intake"
/skill doctidy "status"
```

---

### Q: What is `{TARGET}`?

**A**: `{TARGET}` is a placeholder for your document repository root directory. The skill will determine the actual path via:

1. Cached path from previous use
2. User-specified path
3. Current working directory
4. Prompt user if none available

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

### Q: Score dropped after cleanup?

**A**: Possible reasons:
- New files added without categorization
- Temporary files left behind
- Index files not updated

Run `analyze` to identify the cause.

---

## Duplicates

### Q: How to handle duplicate files?

**A**: Priority order:

1. **User Decision**: You explicitly choose which to keep
2. **Business Relevance**: Keep business-critical files
3. **Recency**: Keep most recently modified
4. **Size**: Keep larger file

Default strategy: Keep business-relevant directory.

---

### Q: Why did my duplicate files get deleted?

**A**: When running `organize`, you were shown a list of planned deletions and asked to confirm. If you approved, duplicates were moved to `_trash/`.

Check `_trash/` to restore if needed.

---

## Intake

### Q: File wasn't auto-categorized correctly?

**A**: The auto-categorization uses keyword matching. If your filename doesn't contain recognized keywords, it will be flagged for manual categorization.

**Tips for better results**:
- Use descriptive filenames with keywords
- Include category indicators (e.g., `demand-mining-...`, `progress-...`)

**To customize**: Edit the keyword table in `commands/intake.md`.

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

### Q: How to add custom categorization rules?

**A**: Edit `commands/intake.md` and add entries to the keyword matching table:

```markdown
| my-keyword | {TARGET}/my-custom-dir/ |
```

---

## Path & Configuration

### Q: How to set target directory?

**A**: Three ways:

1. **Specify in command**: `/skill doctidy "score /path/to/docs"`
2. **Current directory**: cd to target, then run command
3. **First use**: Skill will prompt for path

---

### Q: How to change cached path?

**A**: Run: `/skill doctidy "set target to /new/path"`

---

### Q: Can I use this with any directory structure?

**A**: Yes. The skill is path-agnostic and works with any structure. The default directory structure is recommended but customizable.

---

## For AI Agents

### Q: How do AI agents use this?

**A**: Agents should:

1. Read `{TARGET}/README.md` on first access
2. Check `{TARGET}/_inbox/pending/` for new files
3. Create output in appropriate locations
4. Follow entropy control rules (see `rules.md`)

---

### Q: Agent doesn't update indexes?

**A**: This is by design. Indexes are for quick reference only. If a file isn't found at the indexed location, the agent should scan the directory. No mandatory maintenance required.

---

### Q: Human user doesn't follow conventions?

**A**: The design tolerates this. Features include:
- Index expiration mechanism
- Fallback directory scanning
- Forgiving categorization

---

## Troubleshooting

### Q: Skill not triggering?

**A**: Try:
- Explicit trigger: `/skill doctidy "..."`
- Check skill is installed: `/skills list`
- Check description matches: skill triggers on relevant keywords

---

### Q: Commands not working as expected?

**A**: Check:
1. `{TARGET}` path is correct
2. Required subdirectories exist
3. Run `status` to diagnose

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
