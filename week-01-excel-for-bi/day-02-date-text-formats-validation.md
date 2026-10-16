# Week 1, Day 2 — Date & Text Formats, Find & Replace, Data Validation in Excel

**Date:** 16 Oct 2026
**Focus:** Make dates, numbers and text mean one thing only — then stop bad values getting in at all.

## Why formats and validation matter

Day 1 cleaned data that was already messy. Day 2 is about prevention: if a date column holds real dates in one format, and a Status column only accepts a fixed list, half of tomorrow's cleaning disappears. In BI work, a date stored as text silently breaks sorting, filtering, PivotTables and time-series charts — the numbers look fine but the trend is wrong.

## Key concepts

### 1. Dates are numbers underneath

- Excel stores a date as a serial number (days since 1 Jan 1900 on Windows). Formatting only changes what you see, not the value.
- That is why `12/03/2026` is dangerous: it can mean 12 Mar or 3 Dec depending on locale. Pick one format and stick to it — `YYYY-MM-DD` (e.g. `2026-03-12`) is unambiguous and sorts correctly.
- Check a "date" is really a date: if changing the format to Number shows a number like `46093`, it is a real date; if it stays as text, it is not.

| Task | Excel approach |
|---|---|
| Turn text into a real date | `=DATEVALUE(A2)` (works if the text matches your locale), or Data → Text to Columns → Date |
| Rebuild a date from parts | `=DATE(year, month, day)` |
| Display a date without changing its value | Format Cells → Custom → `yyyy-mm-dd` |
| Show a number as text in a fixed format | `=TEXT(A2,"yyyy-mm-dd")`, `=TEXT(B2,"#,##0.00")` |
| End of a month / add months | `=EOMONTH(A2,0)`, `=EDATE(A2,3)` |

### 2. Text formats: display vs. value

- **Format Cells** (Ctrl + 1) changes display only: `0.35` shown as `35%`, `1250000` shown as `1,250,000`. The underlying value stays the same, so formulas keep working.
- **TEXT()** converts a value into actual text in a chosen format — useful for labels and concatenations (`="Total: "&TEXT(B2,"#,##0")`), but the result can no longer be summed directly.
- `=PROPER()`, `=UPPER()`, `=LOWER()` still apply for standardising case (from Day 1).

### 3. Find & Replace (Ctrl + H) — fast, but respect it

- Use it to fix a repeated typo across a column (e.g. `Chittagong` → `Chattogram`), or to strip a repeated character.
- Turn on **Match entire cell contents** when replacing short values, or `Paid` inside `Unpaid` will get replaced too.
- Use **Find All** first to preview how many cells will change before clicking Replace All.
- Wildcards: `*` = any characters, `?` = one character (e.g. `Dhaka*` finds `Dhaka North` and `Dhaka - North`). Untick them mentally when replacing literal `*` or `?`.
- Find & Replace edits values, not formulas' logic — after a big replace, re-check totals and PivotTables.

### 4. Data Validation — stop bad data at entry

Data → Data Validation. Set the rule *before* people type, not after.

| Rule | Use for |
|---|---|
| List | Fixed choices: Status = `Paid, Pending, Shipped, Cancelled` |
| Whole number / Decimal | Quantity ≥ 1, Unit Price > 0 |
| Date | Order Date between `2026-01-01` and `2026-12-31` |
| Text length | Codes that must be exactly 5 characters |
| Custom (formula) | Anything else, e.g. `=A2<>""` or `=ISNUMBER(A2)` |

- **Input Message** tab: show a hint when the cell is selected ("Pick one status from the dropdown").
- **Error Alert** tab: `Stop` blocks invalid entries; `Warning` lets the user proceed after confirming; `Information` only shows a note.
- **Circle Invalid Data** (Data → Data Validation dropdown) highlights existing cells that break the current rules — a quick audit of an old file.

## Practice (file: day-02-exercise-formats-validation.csv)

1. Open the CSV in Excel. Note that OrderDate_raw mixes `12/03/2026`, `2026-03-15`, `15 Mar 2026` and a text date.
2. Convert OrderDate_raw into a real date column formatted `yyyy-mm-dd`. Use Text to Columns or DATEVALUE, and fix the one row that stays as text.
3. Standardise Product with PROPER, then use Find & Replace (Match entire cell contents) to fix the `Laptob` typo.
4. Add Data Validation to Status: List = `Paid, Pending, Shipped, Cancelled`. Use Circle Invalid Data — one row should be flagged.
5. Format Unit Price as `#,##0.00` and Quantity as a whole number. Add a Total column: `=Quantity * Unit Price`.
6. Sort by the new real Date column and check the order is chronological — it was not before step 2.

## What I learned

- Formatting changes display, not value — so a "date" that is really text will still sort wrong until it is converted.
- Find & Replace is powerful but needs Match entire cell contents and a preview, or it creates new errors while fixing old ones.
- Data Validation with a List + Stop alert is the cheapest quality control in Excel: it prevents the mess Day 1 had to clean.

## What I will try next

- Day 3: XLOOKUP / VLOOKUP, INDEX + MATCH — pulling the right price and category from a lookup table instead of typing them.
