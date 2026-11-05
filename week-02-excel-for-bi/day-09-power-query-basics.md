# Week 2, Day 9 — Power Query Basics: Clean Once, Refresh Forever

**Date:** 5 Nov 2026
**Focus:** Import a messy sales file with Power Query (Get Data), record the cleaning as repeatable steps, and load a clean table — instead of fixing the same mess with formulas every time a new file arrives.

## Why Power Query matters

Days 1–2 cleaned data with formulas: TRIM, Text to Columns, Find & Replace. That works, and it teaches what "clean" means. But it has a weakness: the cleaning lives in the same sheet as the data, mixed in with it. When next month's file arrives with the same problems — extra spaces, mixed-case regions, two date formats — the formula work has to be done again, and one missed copy-down leaves a dirty row hiding in a pivot.

Power Query fixes that by separating the recipe from the data. The source file is never edited. The cleaning is a list of steps (the Applied Steps pane) that runs again, in order, every time the query refreshes. A new file with the same mess goes through the same steps and comes out clean. That repeatability is the whole point: for a BI workflow, cleaning is not a one-off chore, it is a pipeline, and a pipeline should be re-runnable.

## Key concepts

### 1. Get Data, then shape — the query is a recipe, not a copy

Data → Get Data → From Text/CSV opens the Power Query Editor. What you see is a preview, not the loaded table. Every click — remove a column, change a type, trim a field — adds a step to **Applied Steps** on the right. The steps are recorded in M, Power Query's language, which you can read under Home → Advanced Editor. You do not need to write M to use Power Query well, but reading the steps is how you check what a query actually did, and reordering or deleting a step is how you fix it. The golden rule: never edit the source file to make the query work. Fix the query, not the data.

### 2. The standard cleaning steps, in a sensible order

Order matters, because a later step can undo an earlier one (trimming after a merge, for example, misses the merged value). For a typical messy sales file, this order works:

| Step | Where in Power Query | What it fixes in the exercise file |
|---|---|---|
| Promote headers / set types | Home → Use First Row as Headers; change column types | Header row treated as data; numbers stored as text |
| Trim | Transform → Format → Trim | `" Rahim "` and `"Nusrat "` carry leading/trailing spaces (rows 101, 105) |
| Clean | Transform → Format → Clean | Invisible characters pasted in from another system |
| Capitalize Each Word | Transform → Format → Capitalize Each Word | `dhaka` and `CHATTOGRAM` (rows 101, 104) become Dhaka, Chattogram |
| Remove Duplicates | Home → Remove Rows → Remove Duplicates | Order 104 appears twice, identical in every column |
| Replace nulls — deliberately | Right-click a value → Replace Values | Order 109 has no customer name |
| Add a calculated column | Add Column → Custom Column | Line Total = Quantity × Unit Price |
| Close & Load | Home → Close & Load | Lands as a proper Excel Table, ready for a Day 5 pivot |

One caution on Trim + Capitalize: they fix display, not identity. `" Rahim "` and `Rahim` are the same person once trimmed, so a pivot by customer stops splitting him in two — but only if trimming happens before the pivot is built. Cleaning after the analysis is already built is how a dashboard ends up with two Rahims.

### 3. Dates: the locale trap

The exercise file mixes two date formats on purpose: ISO dates (`2026-01-05`) and day-first dates (`15/01/2026`, `10/02/2026`). If Power Query guesses the type with a US locale, `15/01/2026` cannot be a US date (there is no 15th month) and becomes an error, while `10/02/2026` silently becomes 2 October instead of 10 February. The fix is to change the type **using a locale**: right-click the date column → Change Type → Using Locale → Date, English (United Kingdom). Then `15/01/2026` is 15 January and `10/02/2026` is 10 February, and the monthly totals below check out. The lesson generalises: whenever a date column produces errors or suspicious month totals, check the locale before blaming the data.

### 4. Duplicates and blanks are decisions, not defaults

Power Query makes it easy to delete rows, which makes it easy to delete real sales by accident. Two decisions in this file:

- **The duplicate Order 104** is identical in every column — same date, customer, region, product, quantity, price. That is a true duplicate, safe to remove. If the two rows had differed in any column, removing one would need a business reason, not just a click.
- **The blank customer on Order 109** is not a duplicate and not a fake row: it is a real 45.00 Keyboard sale in Chattogram with a missing name. Deleting the row would understate March revenue by 45.00. The better step is Replace Values: null → `Unknown`, which preserves the sale and makes the missing data visible instead of silently dropping it. A blank that is labelled "Unknown" can be followed up; a deleted row cannot.

### 5. Refresh is the payoff

Once the query is loaded, new data flows through the same steps: paste next month's rows into the source CSV (or point the query at the new file), then Data → Refresh All. The new rows are trimmed, capitalised, deduplicated, and totalled by the same recipe — no re-typing formulas, no missed copy-down. Compare that with the Day 1–2 approach, where every new file is a fresh round of manual fixes. The trade-off is honest: Power Query has a learning curve, and a query with twenty steps can be harder to debug than a formula you can see in a cell. For a file cleaned once and never again, formulas are fine. For a file that arrives every week or month, the query wins.

| Need | Formula approach (Days 1–2) | Power Query approach (today) |
|---|---|---|
| Fix spaces / case once | TRIM, PROPER in helper columns | Trim, Capitalize steps, no helper columns |
| Fix it again next month | Redo the helpers, hope nothing is missed | Refresh All — same steps run again |
| Show your working | Formulas visible in cells | Applied Steps list + M code in Advanced Editor |
| Change one cleaning decision | Edit the formula, copy down | Edit, reorder, or delete one step |
| Source file | Often edited in place | Untouched; the query reads it, never writes it |

## Practice (file: day-09-exercise-messy-for-power-query.csv)

The raw file has 11 rows and four planted problems: extra spaces in customer names, mixed-case regions, two date formats, one exact duplicate, and one blank customer.

1. Data → Get Data → From Text/CSV, select the file. Promote the first row to headers and check the column types.
2. Trim the Customer column, then Capitalize Each Word on Region. Confirm `dhaka` → Dhaka and `CHATTOGRAM` → Chattogram.
3. Change the OrderDate type using locale English (United Kingdom). Confirm 15/01/2026 reads as 15 January and 10/02/2026 as 10 February — not October.
4. Remove Duplicates. Row count must fall from 11 to 10, with only one Order 104 remaining.
5. Replace null in Customer with `Unknown`. Confirm Order 109 keeps its 45.00 sale under "Unknown".
6. Add Column → Custom Column, name it Line Total, formula `= [Quantity] * [UnitPrice]`. Close & Load to a Table.
7. Check the loaded table against these verified totals (total 3,090.00):
   - By region: Dhaka 2,050.00; Sylhet 950.00; Chattogram 90.00.
   - By month: January 950.00; February 1,195.00; March 945.00.
   - If any total is off, the usual culprit is step 3 (locale) or a duplicate that survived step 4 — check Applied Steps before re-doing anything by hand.
8. Refresh test: change one UnitPrice in the source CSV, Refresh All, confirm Line Total and the totals update, then change it back and refresh again. The query, not the loaded table, is the thing you maintain.

## What I learned

- Power Query turns cleaning into a re-runnable recipe: the source file stays untouched, the Applied Steps list is the working, and Refresh All re-applies it to new data — which is why it beats re-doing formula fixes on every new file.
- Step order and locale are the two quiet traps: trimming after a merge misses values, and a US-locale date guess turns 10/02/2026 into 2 October instead of 10 February.
- Duplicates and blanks need a business decision first: an identical duplicate can be removed, but a blank customer on a real sale should be labelled "Unknown" so the 45.00 is kept and the gap stays visible.

## What I will try next

- Day 10: Conditional formatting for insight — use the clean Day 9 table to highlight what matters (top sales, falling months) without changing the numbers, and learn where formatting helps a reader and where it just decorates.
