---
name: skyteliste
description: Generate and manage shooting lists (skytelister) for events
---

# Skyteliste Skill — Shooting List Management

Generate shooting lists (skytelister) and output sheets (outputark) for club shooting events. Pulls accepted members from Spond, sorts by seniority from the member reference list, assigns roles and piccili, and exports as XLSX.

**All commands below must be run using the Bash tool.**

## Commands

### Update member reference data

Run with the Bash tool:

```bash
node /workspace/extra/skills/skyteliste/scripts/skyteliste.js update-members [--file <xlsx>]
```

- Parses `Medlemsliste.xlsx` from templates directory into `members.json`.
- Default: uses the bundled `templates/Medlemsliste.xlsx`.
- Run once initially, then again whenever the member list changes.

### Generate shooting list from Spond event

Run with the Bash tool:

```bash
node /workspace/extra/skills/skyteliste/scripts/skyteliste.js generate --event-id <id> [--title "..."] [--number N]
```

- Fetches accepted members from the Spond event.
- Matches Spond names to the member reference list (handles name format differences).
- Sorts by seniority and auto-assigns roles (Protokoll, Formand, Sekretaer).
- Reports unmatched names and category suggestions from comments.
- `--number N` sets the event number (e.g., "Skyting nr. 3").
- **Always run `update-members` first** if members.json doesn't exist yet.

### Show current shooting list

Run with the Bash tool:

```bash
node /workspace/extra/skills/skyteliste/scripts/skyteliste.js show [--file <json>]
```

- Displays the most recent shooting list (or specify a file).
- Shows position, name, seniority, category, and comment for each participant.

### List all generated shooting lists

Run with the Bash tool:

```bash
node /workspace/extra/skills/skyteliste/scripts/skyteliste.js list
```

### Identify and place piccili

Run with the Bash tool:

```bash
node /workspace/extra/skills/skyteliste/scripts/skyteliste.js piccoli [--positions 5,15,25] [--count N]
```

- Without `--positions`: shows the 4 least senior members who will be piccoli.
- With `--positions`: places them at the specified positions in the list.
- The `--positions` parameter sets WHERE in the list to place them (evenly distributed), NOT who they are.
- Default count: 4. Never assigns: Mogens L. Mathiesen, Andreas Petteroe, Truls Ryder.

### Add a member

Run with the Bash tool:

```bash
node /workspace/extra/skills/skyteliste/scripts/skyteliste.js add-member --name "Name" --to <position>
node /workspace/extra/skills/skyteliste/scripts/skyteliste.js add-member --name "Name" --after "Member"
```

- Looks up the person in `members.json` with correct seniority and handicap.
- **Use this for club members.** Use `add-guest` only for non-members.

### Add a guest

Run with the Bash tool:

```bash
node /workspace/extra/skills/skyteliste/scripts/skyteliste.js add-guest --name "Name" --after "Member"
```

### Move a participant

Run with the Bash tool:

```bash
node /workspace/extra/skills/skyteliste/scripts/skyteliste.js move --name "Name" --after "Member"
node /workspace/extra/skills/skyteliste/scripts/skyteliste.js move --name "Name" --to <position>
```

### Remove a participant

Run with the Bash tool:

```bash
node /workspace/extra/skills/skyteliste/scripts/skyteliste.js remove --name "Name"
```

### Change participation category

Run with the Bash tool:

```bash
node /workspace/extra/skills/skyteliste/scripts/skyteliste.js set-category --name "Name" --category "kun skyting"
```

- Categories: `skyting_og_middag` (default), `kun_skyting`, `kun_middag`

### Assign role

Run with the Bash tool:

```bash
node /workspace/extra/skills/skyteliste/scripts/skyteliste.js set-role --name "Name" --role "Piccolo"
```

- Setting "Protokoll" automatically moves the person to position #1.

### Import from exported XLSX

Run with the Bash tool:

```bash
node /workspace/extra/skills/skyteliste/scripts/skyteliste.js import --file <xlsx-path>
```

- Reads back an exported Skyteliste XLSX into working JSON for further editing.
- Use this when the JSON was deleted after export and you need to make changes.
- File path on NAS: `/data/club-docs-rw/Skytelister/Skyteliste-YYYY-MM-DD.xlsx`

### Export as XLSX

Run with the Bash tool:

```bash
node /workspace/extra/skills/skyteliste/scripts/skyteliste.js export [--file <json>] [--output <path>]
```

- Generates XLSX with two sheets: Skyteliste + Outputark.
- Default output: `/data/club-docs-rw/Skytelister/Skyteliste-YYYY-MM-DD.xlsx`

### Generate email body

Run with the Bash tool:

```bash
node /workspace/extra/skills/skyteliste/scripts/skyteliste.js email-body [--file <json>]
```

- Outputs ready-to-use HTML email body with all roles, names, and counts.
- Use the output directly as `--body` in the email send command.
- **ALWAYS use this command when emailing a shooting list — never compose the email text manually.**

### OCR protocol photos and auto-apply scores (read-scores)

Run with the Bash tool:

```bash
node /workspace/extra/skills/skyteliste/scripts/skyteliste.js read-scores --photos <path1,path2,...> --apply
```

- OCRs protocol photos using Google Cloud Vision API (DOCUMENT_TEXT_DETECTION).
- Automatically converts HEIC/HEIF to JPEG before OCR.
- Matches OCR names to skyteliste participants (fuzzy matching, handles Norwegian chars).
- **With `--apply`**: Auto-normalizes OCR shot data and saves scores directly to the skyteliste JSON. Outputs a compact result table.
- **Without `--apply`**: Outputs raw suggested JSON for manual review.
- Requires `GOOGLE_VISION_API_KEY` env var.
- **ALWAYS use `--apply` flag** — this eliminates the need for manual JSON editing.
- After `--apply`, run `export` to generate XLSX with scores populated in Outputark.
- If scores need correction: `clear-scores` then `apply-scores --data '<json>'` for specific participants.

### Apply scores manually (apply-scores)

Run with the Bash tool:

```bash
node /workspace/extra/skills/skyteliste/scripts/skyteliste.js apply-scores --data '<json>'
```

- For manually entering or correcting scores for specific participants.
- Accepts raw OCR strings — the script normalizes to 0/1 arrays automatically.
- Auto-normalizes: strips separators, maps `l/I/|/!` -> 1, `O/o` -> 0.
- Cross-checks against `sums` and `total` if provided.

**Input JSON format:**
```json
{"Wilhelmsen, Thomas": {"poppe": "11101-11110", "double": "11.10.10.11.10", "tenMeter": "1111011111", "side": "01101-10101", "tower": "11010 11011", "sums": [8, 7, 9, 6, 7], "total": 37}}
```

### Clear all scores (clear-scores)

Run with the Bash tool:

```bash
node /workspace/extra/skills/skyteliste/scripts/skyteliste.js clear-scores
```

- Removes all score data from the current skyteliste JSON.
- Use this when you need to redo score entry from scratch.
- After clearing, run `apply-scores` again with corrected data.

### Show results and placements (scores)

Run with the Bash tool:

```bash
node /workspace/extra/skills/skyteliste/scripts/skyteliste.js scores [--date YYYY-MM-DD] [--file <json>]
```

- Displays results table: per-series scores, total, placement (1-6), HCP, netto, HCP placement.
- `--date YYYY-MM-DD`: finds the skyteliste matching that date.
- Without arguments: shows results for the most recent skyteliste.
- Use when the user asks about results, who won, or placements for a specific date.

### Name overrides (for ambiguous matches)

Run with the Bash tool:

```bash
node /workspace/extra/skills/skyteliste/scripts/skyteliste.js set-override --spond "Spond Name" --member "LastName, FirstName"
node /workspace/extra/skills/skyteliste/scripts/skyteliste.js list-overrides
node /workspace/extra/skills/skyteliste/scripts/skyteliste.js remove-override --spond "Spond Name"
```

- When Spond name matching picks the wrong person (e.g., father vs son with similar names), use `set-override` to create a persistent mapping.
- Overrides are checked first during `generate`, before the automatic matching algorithm.
- Stored in `data/name-overrides.json`.

## Typical workflow

1. `update-members` (once, or when member list changes)
2. `generate --event-id <id> --number N` (from Spond event)
   - **Or** `import --file <xlsx>` to reload a previously exported list
3. Review with `show`, adjust with `set-category`, `piccoli`, `add-guest`, etc.
4. **(Optional) Score entry after the event:**
   a. `read-scores --photos <photo> --apply` — OCR + auto-apply scores in one step
   b. Review the compact result table, correct any errors with `apply-scores --data '<json>'`
5. **(Optional)** `scores` to review results and placements before export
6. `export` to generate XLSX (with scores populated in Outputark if step 4 was done)
7. `email-body` to generate email text, then send with attachment
7. Delete working JSON when done (agent cleans up manually)

## Protocol Format — How to Interpret OCR Text

The shooting protocol book has these columns per participant:

| Column | Shots | Description |
|--------|-------|-------------|
| Poppe | 10 | Written as "11101-11110" (dash is visual grouping, NOT a separator) |
| Double | 10 | Same format, e.g. "11.10.10.11.10" (dots are visual grouping) |
| 10 m | 10 | Same format |
| Side | 10 | Same format |
| Tarn side | 5 | First 5 shots of Tarn series |
| Tarn mot | 5 | Last 5 shots of Tarn series |

**CRITICAL: Each series is ALWAYS 10 shots (0 or 1). "11101-11110" is ONE series of 10 shots, NOT two series of 5.**

- Tarn is split into two physical columns ("Tarn side" + "Tarn mot") but combines into one 10-shot `tower` array.
- Each dash, dot, or space in the OCR is just visual grouping — ignore them, extract only the 0s and 1s.
- After each series, a **sum** appears (count of 1s in that series, range 0-10).
- After some series, a **running total** appears (cumulative sum across series).
- Names are in the leftmost column, numbered 1-N.

**OCR interpretation example:**
```
Thomas Wilhelmsen  11101-11110 8  11.10.10.11.10 7  15  1111011111 9  ...
```
For each participant, extract:
- The shot strings (groups of 0s and 1s with dots/dashes/spaces as visual separators)
- The sum after each series (single number 0-10)
- The running total (cumulative, appears after some series)
- Keep the raw OCR strings — do NOT convert to arrays. The script normalizes automatically.

Output JSON for this example:
```json
{"Wilhelmsen, Thomas": {"poppe": "11101-11110", "double": "11.10.10.11.10", "tenMeter": "1111011111", "side": "...", "tower": "...", "sums": [8, 7, 9, ...], "total": 37}}
```

**Total score per participant is typically 25-45 (sum of all 5 series). If your totals are below 20, you are misreading the protocol.**

**OCR character mapping (handled automatically by the script):**
- `1`, `l`, `I`, `|`, `!`, `7` -> hit (1)
- `0`, `O`, `o` -> miss (0)
- Dots, dashes, spaces, commas -> stripped (visual grouping only)
- Each series MUST have exactly 10 shot characters after stripping separators

## Important Notes

- Name matching handles "FirstName LastName" (Spond) to "LastName, FirstName" (Medlemsliste) conversion automatically.
- Participants are sorted by seniority (lowest number = most senior).
- Category suggestions from Spond comments are shown but NOT auto-applied for ambiguous cases — confirm with user.
- The Outputark sheet has columns B and C swapped (Ansiennitet, Medlem) compared to Skyteliste (Medlem, Ansiennitet).
