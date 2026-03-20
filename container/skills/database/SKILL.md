---
name: database
description: Query 25 years of club historical data (scores, attendance, finances, board, members)
---

# Database Skill — Club Historical Data

Query 25 years (2001-2025) of club data: shooting results, attendance, trophies, payments, wages, and vendor costs. All data is stored in a local SQLite database.

**All commands below must be run using the Bash tool.**

## Query Commands

### Season overview

Run with the Bash tool:

```bash
node /workspace/extra/skills/database/scripts/database.js stats
```

- Shows all seasons with event count, active members, average score, roses, lice.

### Member profile

Run with the Bash tool:

```bash
node /workspace/extra/skills/database/scripts/database.js member "Name"
node /workspace/extra/skills/database/scripts/database.js member --name "Name"
```

- Fuzzy name search (partial match).
- Shows season history, trophies (top 6), and best individual scores.

### Top scores

Run with the Bash tool:

```bash
node /workspace/extra/skills/database/scripts/database.js top-scores [--year YYYY] [--limit N]
```

- Without `--year`: all-time average ranking (across all seasons, min 3 events/season).
- With `--year`: single-season ranking.
- Default limit: 20.

### Attendance statistics

Run with the Bash tool:

```bash
node /workspace/extra/skills/database/scripts/database.js attendance [--year YYYY] [--name "..."]
```

- `--year`: best attendees for that season.
- `--name`: per-member attendance across all years.
- Neither: all-time attendance leaders.

### Full season report

Run with the Bash tool:

```bash
node /workspace/extra/skills/database/scripts/database.js season --year YYYY
```

- Top performers, event list with dates, scores, attendance counts.

### Head-to-head comparison

Run with the Bash tool:

```bash
node /workspace/extra/skills/database/scripts/database.js compare "Name1" "Name2"
```

- Side-by-side season averages and head-to-head record in shared events.

### Trophy winners

Run with the Bash tool:

```bash
node /workspace/extra/skills/database/scripts/database.js trophies [--name "..."] [--trophy "..."]
```

- No args: overview of all trophy winners.
- `--name`: all trophies won by a member.
- `--trophy`: winners of a specific trophy across years.
- Trophy names: arspokalen, honorporkalen, rosepokalen, lusepokalen, hra_pokalen, apningsskyting, st_hans, arild_berg, avslutning, faktisk_snitt, portvinspokalen, skaugenpokalen, hostpokalen, thranes_solvbrett.

### Board composition

Run with the Bash tool:

```bash
node /workspace/extra/skills/database/scripts/database.js board [--year YYYY]
```

- With `--year`: board members and roles for that season.
- Without `--year`: leadership history — who has served in each role across all years.
- Data from GF Protokoll (elections) and Aarsberetning (board listings).

### Fee rates

Run with the Bash tool:

```bash
node /workspace/extra/skills/database/scripts/database.js fees [--year YYYY]
```

- With `--year`: all fee rates for that season.
- Without `--year`: Aarskontingent evolution over all years.
- Data from GF Protokoll (kontingent decisions).

### Member directory

Run with the Bash tool:

```bash
node /workspace/extra/skills/database/scripts/database.js directory [--year YYYY] [--name "..."]
```

- `--year`: member directory snapshot for that season (category, title, member since).
- `--name`: a specific member's directory history across years.
- Neither: overview of member counts per year and category.
- Data from annual Medlemmer & Love PDFs (2012-2025).

### Financial statements (Aarsregnskap)

Run with the Bash tool:

```bash
node /workspace/extra/skills/database/scripts/database.js accounts --year YYYY [--type resultat|balanse|notes|all]
```

- `--type resultat`: income statement (Resultatregnskap) with current and prior year amounts.
- `--type balanse`: balance sheet with current and prior year amounts.
- `--type notes`: explanatory notes with sub-items.
- `--type all` or no type: all three sections.
- Data from audited annual accounts PDFs (2018-2025).

### Financial summary

Run with the Bash tool:

```bash
node /workspace/extra/skills/database/scripts/database.js financial --year YYYY
```

- Payment overview (billed, paid, outstanding), vendor costs, wage costs.
- Wage data excludes PII (no personal IDs or bank accounts).

### Raw SQL query

Run with the Bash tool:

```bash
node /workspace/extra/skills/database/scripts/database.js sql "SELECT ..."
```

- Read-only: only SELECT, PRAGMA, and EXPLAIN allowed.
- Tables: members, seasons, events, attendance, scores, season_results, trophies, payments, transactions, vendor_costs, wages, member_directory, board_members, fee_rates, financial_statements, financial_notes, member_stats, import_log.
- Use this for ad-hoc questions not covered by the built-in commands.

## Import Commands

These are for populating the database. Run once after deployment, then again when new data is available.

### From XLSX spreadsheets

```bash
node /workspace/extra/skills/database/scripts/database.js import-results --year YYYY
node /workspace/extra/skills/database/scripts/database.js import-results --all
node /workspace/extra/skills/database/scripts/database.js import-historikk
node /workspace/extra/skills/database/scripts/database.js import-payments --year YYYY
node /workspace/extra/skills/database/scripts/database.js import-wages --year YYYY
node /workspace/extra/skills/database/scripts/database.js import-vendors --year YYYY
```

- `import-results`: Parses CJK - {year} Deltagelse & Resultater.xlsx (detailed per-event data).
- `import-historikk`: Parses Resultater historikk.xlsx (summary data, 2001-2025).

### From PDF documents

```bash
node /workspace/extra/skills/database/scripts/database.js import-accounts --year YYYY
node /workspace/extra/skills/database/scripts/database.js import-accounts --all
node /workspace/extra/skills/database/scripts/database.js import-protokoll --year YYYY
node /workspace/extra/skills/database/scripts/database.js import-protokoll --all
node /workspace/extra/skills/database/scripts/database.js import-beretning --year YYYY
node /workspace/extra/skills/database/scripts/database.js import-beretning --all
node /workspace/extra/skills/database/scripts/database.js import-directory --year YYYY
node /workspace/extra/skills/database/scripts/database.js import-directory --all
```

- `import-accounts`: Parses Aarsregnskap PDFs — P&L, balance sheet, notes (2018-2025).
- `import-protokoll`: Parses GF Protokoll PDFs — board elections, fee rates (2016-2026).
- `import-beretning`: Parses Aarsberetning PDFs — extra trophies, board listings, member stats (2021-2025).
- `import-directory`: Parses Medlemmer & Love PDFs — member directory with categories and titles (2012-2025).
- Requires `pdftotext` (poppler-utils) installed in the container.

### Management

```bash
node /workspace/extra/skills/database/scripts/database.js import-all
node /workspace/extra/skills/database/scripts/database.js import-status
node /workspace/extra/skills/database/scripts/database.js rebuild
```

- `import-all`: Runs all XLSX and PDF imports.
- `import-status`: Shows row counts for all tables and recent import log.
- `rebuild`: Deletes database and recreates schema.

## Database Schema

Key tables and their relationships:

- **members**: name, seniority, email, handicap
- **seasons**: year, num_events, venue
- **events**: season, number, date, name, type
- **attendance**: event, member, category (skyting_og_middag/kun_skyting/kun_middag), role
- **scores**: event, member, poppe/double/ten_meter/side/tower, total, roses, lice, placement, handicap, netto
- **season_results**: season, member, events_attended, counting_average, hit_percentage, roses, lice
- **trophies**: season, member, trophy name, rank, score
- **payments**: season, member, kontingent, prepaid, total_due, total_paid, outstanding
- **vendor_costs**: season, vendor, date, amount, vat, total
- **wages**: season, name, period, hours, hourly_rate, gross_total, total_cost (no PII)
- **member_directory**: season, member, category (aeresmedlem/innbudt/ordinaer), title, member_since
- **board_members**: season, member, role (formand/viceformand/kasserer/revisor/banemester/klubdoktor/klubphotograph), term_years
- **fee_rates**: season, fee_type (aarskontingent/engangskontingent/middag/skyting etc.), amount, note
- **financial_statements**: season, statement_type (resultat/balanse), section, line_item, note_ref, amount, prior_year_amount
- **financial_notes**: season, note_number, title, line_item, amount, prior_year_amount
- **member_stats**: season, category (aeresmedlem/innbudt/over_80/utlandet/tellende/total), count

## Notes

- Names are in "LastName, FirstName" format throughout.
- The database file is at `/workspace/extra/skills/database/data/cjk.sqlite` (~250KB).
- Uses sql.js (SQLite compiled to WebAssembly) — no native dependencies.
- Requires `pdftotext` (poppler-utils) for PDF imports.
- Historical data (2001-2020) comes from summary sheets; detailed per-shot data is only available for years with Deltagelse & Resultater files (2021-2025).
- PDF data covers: financial statements (2018-2025), board elections (2016-2026), annual reports (2021-2025), member directories (2012-2025).
