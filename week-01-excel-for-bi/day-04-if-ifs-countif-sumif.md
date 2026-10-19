# Week 1, Day 4 — IF, IFS, COUNTIF / SUMIF in Excel

**Date:** 18 Oct 2026
**Focus:** Label each order (High / Medium / Standard value) and summarise revenue by category — without filtering, sorting, or counting by hand.

## Why conditional formulas matter

Day 3 fetched values from another table. Today is about decisions and summaries: given a table of orders, which ones are high-value, and how much revenue came from each category? Doing that by filtering and hand-counting works once, but it breaks the moment the data changes — new rows get missed, and nobody can reproduce the count. `IF` / `IFS` put the labelling rule inside the sheet, so every row is classified the same way and re-classifies itself when the data changes. `COUNTIF` / `SUMIF` do the same for summaries: the count or total is a formula, not a number typed after eyeballing a filter. In BI work this is the same idea as `CASE WHEN` and `GROUP BY` in SQL — the logic lives in the query, not in someone's head.

## Key concepts

### 1. IF — one decision, two outcomes

```excel
=IF(condition, value_if_true, value_if_false)
```

Example: flag orders worth 500 or more (Line Total in `F2`):

```excel
=IF(F2>=500, "High", "Standard")
```

- The condition is any comparison: `>=`, `<`, `=`, `<>`, or a comparison of text (`A2="Hardware"`).
- The result can be text, a number, a date, or another formula — but keep the two branches the same type, or later sums will surprise you (e.g. `""` in the false branch makes a column part-text).
- Never compare a number stored as text: `"500" >= 500` behaves unexpectedly. Clean the data first (Day 1) so numbers are numbers.

### 2. Nested IF vs IFS — stop nesting at two levels

A three-band label with nested IF:

```excel
=IF(F2>=500, "High", IF(F2>=150, "Medium", "Standard"))
```

The same rule with IFS — one condition per line, read top to bottom, first match wins:

```excel
=IFS(F2>=500, "High", F2>=150, "Medium", TRUE, "Standard")
```

- `IFS` tests conditions in order and returns the result of the first true one, so put the strictest condition first. `IFS(F2>=150, "Medium", F2>=500, "High", ...)` would never return "High", because 850 is also `>= 150`.
- The final `TRUE, "Standard"` is the catch-all — without it, a row matching no condition returns `#N/A`.
- Rule of thumb: one decision → `IF`; two bands → `IF`; three or more bands → `IFS`. Deep nested IFs are hard to read and easy to get wrong.
- Note: `IFS` needs Excel 2019 / Microsoft 365. In older Excel, nested IF is the fallback.

### 3. COUNTIF / SUMIF — summarise by a condition

```excel
=COUNTIF(range, criteria)
=SUMIF(range, criteria, sum_range)
```

Examples: count Hardware orders and total Hardware revenue (Category in `B2:B11`, Line Total in `F2:F11`):

```excel
=COUNTIF(B2:B11, "Hardware")
=SUMIF(B2:B11, "Hardware", F2:F11)
```

- `criteria` is almost always in quotes: `"Hardware"`, `">=500"`, `"H*"`. A criteria that starts with `>` / `<` / `=` must be text, even when it compares numbers.
- Wildcards work in text criteria: `*` matches any characters (`"H*"` matches Hardware), `?` matches one character. To match a literal `*` or `?`, use `~*` / `~?`.
- Cell references as criteria need `&`: `=COUNTIF(B2:B11, A14)` works for an exact match, but `=COUNTIF(D2:D11, ">" & A14)` is the way to compare Quantity against a threshold in a cell.
- In `SUMIF`, `sum_range` must line up row-for-row with `range` — Excel pairs rows by position, not by any key. If `sum_range` is shorter, the result is silently wrong. Keep both ranges the same size and, ideally, the same rows.
- COUNTIF / SUMIF are case-insensitive: `"hardware"` counts `Hardware`.

### 4. COUNTIFS / SUMIFS — more than one condition

```excel
=COUNTIFS(criteria_range1, criteria1, criteria_range2, criteria2, ...)
=SUMIFS(sum_range, criteria_range1, criteria1, criteria_range2, criteria2, ...)
```

Example: revenue from Accessories orders with quantity 5 or more (Quantity in `D2:D11`):

```excel
=SUMIFS(F2:F11, B2:B11, "Accessories", D2:D11, ">=5")
```

- All conditions are AND — a row must meet every one. There is no built-in OR version; for OR, add two separate COUNTIF / SUMIF results.
- Watch the argument order: `SUMIFS` takes `sum_range` first, while `SUMIF` takes it last. Mixing them up is the most common SUMIFS mistake.

| Need | Use |
|---|---|
| One decision, two outcomes | `IF` |
| Three or more bands | `IFS` (or nested IF in old Excel) |
| Count rows meeting one condition | `COUNTIF` |
| Total a column for rows meeting one condition | `SUMIF` |
| Count / total with several conditions (AND) | `COUNTIFS` / `SUMIFS` |
| OR across conditions | Add two COUNTIF / SUMIF results |

## Practice (file: day-04-exercise-orders-summary.csv)

1. Open the CSV in Excel. Add a Line Total column: `=Quantity * UnitPrice` (Quantity in `D`, UnitPrice in `E` — adjust to your layout).
2. Add a Value Band column with `IFS`: Line Total `>= 500` → `High`, `>= 150` → `Medium`, otherwise `Standard`. Check: 2 High, 4 Medium, 4 Standard.
3. Re-do the band with nested `IF` and confirm every row matches the `IFS` version. Then ask: which formula would you rather audit in six months?
4. In a small summary block, use `COUNTIF` and `SUMIF` to count orders and total revenue per category. Expected: Hardware — 4 orders, 2240.00; Accessories — 6 orders, 762.50; grand total 3002.50.
5. Use `COUNTIF` with `">=5"` on Quantity: expected 3 orders with quantity 5 or more.
6. Use `SUMIFS` for Accessories revenue where Quantity `>= 5`: expected 457.50. Then try a threshold stored in a cell instead: `">=" & $H$2`.
7. Deliberately break it, then fix it: change a category to lowercase `hardware` and confirm COUNTIF still counts it (case-insensitive); then change a UnitPrice to text (e.g. type `850` with a leading apostrophe) and watch the Line Total and SUMIF change — a reminder that Day 1 cleaning matters.

## What I learned

- `IFS` with a `TRUE` catch-all is easier to read and audit than three nested IFs, and condition order matters — strictest first, because the first true condition wins.
- `COUNTIF` / `SUMIF` turn a hand-counted summary into a formula that stays correct when rows are added; `SUMIFS` takes the sum range first, unlike `SUMIF`.
- Criteria quirks are the real lesson: comparisons go in quotes, cell thresholds need `&`, and `SUMIF` pairs ranges by position, so mismatched range sizes fail silently.

## What I will try next

- Day 5: PivotTables — grouping, slicers, calculated fields — the same summaries as today, but interactive and without writing a formula per category.
