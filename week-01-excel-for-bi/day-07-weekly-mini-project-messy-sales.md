# Week 1, Day 7 — Weekly Mini-Project: Clean + Summarise a Messy Sales File

**Date:** 21 Oct 2026
**Focus:** End-to-end run of the Week 1 chain: take the messy 12-row sales export in `day-07-exercise-messy-sales.csv`, clean it (Days 1–2), enrich it with formulas (Days 3–4), summarise it with PivotTables (Day 5), and present it as a one-page dashboard (Day 6) — then prove the cleaning didn't change the truth.

## Why a mini-project matters

Days 1–6 each added one tool. Real work never arrives as one tool: it arrives as a file someone exported badly — duplicate rows, dates as text, "Chittagong" spelled two ways, a blank quantity — and the job is the whole chain: clean it, check it still adds up, summarise it, and hand over a page a manager can read. This project is the Week 1 chain, end to end, in about an hour. Everything it uses was covered this week; nothing new is introduced.

## Key concepts

### 1. The chain, in order

| Step | Uses | Output |
|---|---|---|
| Triage | — | A written list of defects, plus raw row counts |
| Clean | Days 1–2 | One clean table: real dates, one spelling per region, no duplicates |
| Enrich | Days 3–4 | Line Total, Category (XLOOKUP), flags (IF) |
| Summarise | Day 5 | PivotTables: revenue by month, by region, by category |
| Present | Day 6 | One-page dashboard with a slicer |
| Reconcile | — | Control totals: raw vs cleaned, every summary tied back |

### 2. Triage first: know what's wrong before touching anything

- Open the CSV, convert it to a Table (Ctrl + T) on a *copy* — never clean the original sheet. The raw file is the audit trail.
- Skim each column and write down the defects. This file has eight: row 102 has a leading space in Customer and a lowercase region; the third data row is an exact duplicate of the Order 101 row (identical OrderID and every other column); row 104's date is text ("Mar 3 2026") and its region is "Chittagong"; row 106 has no Quantity; the tenth data row duplicates the Order 109 row (identical on every column); row 111 uses "Chittagong" again; row 112's region has extra spaces (" Dhaka ").
- Count the rows: 12 raw. Write it down — the reconciliation at the end starts here.

### 3. Cleaning (Days 1–2)

- TRIM Customer and Region (`=TRIM(...)`, paste as values) — fixes rows 102 and 112.
- Standardise the region: Find & Replace "Chittagong" → "Chattogram" with **Match entire cell contents** on, then fix the case of "dhaka" and "SYLHET" (Day 1's PROPER/UPPER trick). Expected final values: Dhaka, Chattogram, Sylhet only.
- Dates: `2026-01-05`-style entries are already real dates. `05/02/2026` on a Bangladesh (DD/MM) locale is **5 Feb 2026** — state your locale assumption explicitly, because on a US machine DATEVALUE would read it as May 2. "Mar 3 2026" is text → `=DATEVALUE()` or Data → Text to Columns → Date (Day 2). Verify with the serial-number test: format as Number; real dates show ~46xxx.
- Remove Duplicates across all columns: takes out the duplicated Order 101 row and the duplicated Order 109 row → 10 rows. Sanity check first: the duplicate is identical on every column, including OrderID — a true double-export, safe to remove. A row that *looked* similar but carried a different OrderID would be a separate order and must stay.

### 4. The missing quantity — decide a policy, don't just delete

- Row 106 (Alam Mart, Pen) has no Quantity. Deleting the row hides a real order; inventing a number fabricates data. The honest policy: a Data Quality column with `=IF(ISBLANK(QuantityCell),"Incomplete","OK")`, and let the blank count as 0 in Line Total (blank × price is 0 in Excel, or be explicit with `=IF(ISBLANK(...),0,Quantity*UnitPrice)`). The order stays visible with a flag — the reader decides, not you.
- This is the judgement call the week was building toward: cleaning is not just formulas, it's keeping the numbers auditable.

### 5. Enrichment (Days 3–4)

- Line Total `=Quantity*UnitPrice`. Expected grand total: **6,860** — this number is the control total everything later must match.
- Category via XLOOKUP from a small reference table you type on a "Lookup" sheet: Notebook → Paper Goods; Pen → Writing; Eraser → Writing.
- High-value flag: `=IF(LineTotal>=1000,"High value","Standard")` → 2 high-value orders (101 and 102).
- Pre-pivot checks with SUMIF/COUNTIF (Day 4): `=SUMIF(RegionRange,"Dhaka",LineTotalRange)` should read **3,750**. If the later PivotTable disagrees, the cleaning step has a bug — find it, don't adjust the pivot.

### 6. Summarise and present (Days 5–6)

- PivotTable 1: OrderDate grouped by Month in Rows, Sum of Line Total → Jan 1,200 / Feb 1,250 / Mar 2,360 / Apr 2,050.
- PivotTable 2: Region in Rows → Dhaka 3,750 / Chattogram 2,510 / Sylhet 600.
- PivotTable 3: Category in Rows → Paper Goods 2,760 / Writing 4,100.
- Dashboard page: a title that states the finding ("Dhaka drove 55% of cleaned revenue; March was the strongest month"), three KPIs at the top (Total 6,860 / Orders 10 / Average order value 686.00), a monthly column chart, a region bar chart, and one Region slicer connected to everything (Day 6's honesty checks apply: refresh, click every slicer option, clear filters).

### 7. Reconcile — prove the cleaning didn't change the truth

| Check | Expected |
|---|---|
| Raw rows | 12 |
| Rows after dedupe | 10 |
| Raw revenue sum (all 12 rows) | 8,380 |
| Removed duplicates | 1,200 (Order 101, duplicated) + 320 (Order 109, duplicated) = 1,520 |
| Cleaned total | 6,860 (= 8,380 − 1,520) ✓ |
| Month pivot total | 6,860 ✓ |
| Region pivot total | 6,860 ✓ |
| Category pivot total | 6,860 ✓ |

- If the three pivots don't all hit 6,860, the cleaning introduced an error — go back to §3, don't touch the pivots. Reconciliation is what makes a cleaned file trustworthy: every summary ties back to the raw file minus documented removals.

## Practice (file: day-07-exercise-messy-sales.csv)

1. Open the CSV in Excel. Put the raw data on a "Raw" sheet (untouched) and work on a copy.
2. **Triage:** list every defect you can find *before* fixing any. Aim for the eight in §2.
3. **Clean:** TRIM text columns, standardise regions to Dhaka / Chattogram / Sylhet, convert the two text dates to real dates (state your locale assumption), remove the 2 duplicate rows. 10 rows should remain.
4. **Enrich:** add Line Total, Category (build your own lookup table and use XLOOKUP), High-value flag (`>=1000`), and Data Quality flag for the blank quantity.
5. **Check:** with SUMIF/COUNTIF, confirm Dhaka revenue = 3,750, total orders = 10, incomplete rows = 1.
6. **Summarise:** build the three PivotTables and confirm each totals 6,860.
7. **Present:** build the one-page dashboard with the Region slicer; the title must state a finding, not a label.
8. **Reconcile:** fill in the §7 table and confirm every line.

Expected answers: 10 rows; total 6,860. Months — Jan 1,200 / Feb 1,250 / Mar 2,360 / Apr 2,050. Regions — Dhaka 3,750 / Chattogram 2,510 / Sylhet 600. Categories — Paper Goods 2,760 / Writing 4,100. High-value orders: 2. Incomplete rows: 1.

## What I learned

- A week's tools only pay off as a chain: triage → clean → enrich → summarise → present → reconcile. Each step feeds the next, and skipping triage is how "clean" files end up wrong.
- The cleaning decisions that matter are judgement calls, not formulas: blank quantity → flag and keep the row (don't delete, don't invent), true duplicate → remove only after checking the OrderID matches.
- Reconciliation (control totals) is what makes a cleaned file trustworthy. Raw 8,380 minus documented duplicate removals 1,520 equals cleaned 6,860 — and all three pivots must land on the same number.

## What I will try next

- Week 2: advanced PivotTables and Power Query basics — redo this same mini-project in Power Query and compare the effort against the formula-based approach.
