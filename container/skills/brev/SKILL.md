---
name: brev
description: Generate club letters on Christiania Jaegerklub letterhead
---

# Brev Skill — Letter Generation on Club Letterhead

Generate `.docx` letters on Christiania Jaegerklub letterhead. The output includes the club logo, correct fonts, A4 page layout, and consistent formatting matching previous club correspondence.

**All commands below must be run using the Bash tool.**

## Generate a letter

Run with the Bash tool:

```bash
node /workspace/extra/skills/brev/scripts/docx-gen.js generate --input <markdown-file> --output <path.docx>
```

- `--input`: Path to a markdown file with the letter body
- `--output`: Path for the generated .docx file (e.g. `/data/club-docs-rw/Brev/Vaarbrev-2026.docx`)
- Without `--output`: writes binary .docx to stdout (not useful for the agent)

## Input format

Write the letter body as simple markdown. Each line becomes a paragraph.

```markdown
Oslo, 1. mars 2026

**Til Medlemmene,**

Christiania Jaegerklubs 149. sesong starter mandag 27. april kl. 17:30 paa Loevenskioldbanen.

**Skytedager 2026**

Paamelding senest fredag kl 12:00 via Spond.

- Spisekontingent	kr. 450,-
- Skytekontingent	kr. 600,-
- Skyting og middag	kr. 850,-

Med vennlig hilsen,

Dag Klaveness
Kasserer
```

**Formatting rules:**
- `**text**` -> bold text
- Blank line -> empty paragraph (spacing)
- `- item` or `* item` -> bulleted list item (dash style)
- Tab character (`\t`) -> tab stop (for aligning amounts)
- Place+date line (e.g. "Oslo, 1. mars 2026") -> automatically right-justified
- All other lines -> regular justified text

## Typical workflow

1. Draft the letter content as markdown (write to a temp file)
2. Generate: `node /workspace/extra/skills/brev/scripts/docx-gen.js generate --input /tmp/letter.md --output /data/club-docs-rw/Brev/2026/Vaarbrev-2026.docx`
3. Confirm the file was created successfully

## Important notes

- The template includes the club logo — do NOT add any logo text to the markdown
- Start with place+date (e.g. "Oslo, 1. mars 2026") — it is automatically right-justified
- Then the greeting (e.g. "**Til Medlemmene,**")
- Leave a blank line after the greeting for spacing
- Use tabs for price alignment in fee lists
- Output path should be under `/data/club-docs-rw/Brev/` organized by year
