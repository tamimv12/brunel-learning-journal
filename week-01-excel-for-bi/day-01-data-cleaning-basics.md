# Week 1, Day 1 — Data Cleaning Basics in Excel (for BI)

**Date:** 16 Oct 2026  
**Focus:** Clean data before any analysis. A dashboard built on dirty data gives confident wrong answers.

## Why cleaning comes first

In BI work, 60–80% of time is often spent getting data ready. If customer names, dates or amounts are inconsistent, pivots, charts and KPIs will all be wrong — even if formulas are correct.

## 5 common dirty-data problems

1. **Extra spaces:** "  Dhaka " vs "Dhaka" — Excel treats them as different values.
2. **Duplicates:** Same sale entered twice inflates revenue.
3. **Inconsistent text:** "Dhaka", "DHAKA", "dhaka" split one city into three groups.
4. **Mixed date formats:** 12/03/2026 can mean 12 Mar or 3 Dec depending on format.
5. **Blank / placeholder values:** Empty cells, "N/A", "-" where a number should be.

## Excel tools used today

| Problem | Excel fix |
|---|---|
| Extra spaces | `=TRIM(A2)` — removes leading/trailing + double spaces |
| Case inconsistency | `=UPPER()`, `=LOWER()`, `=PROPER()` |
| Duplicates | Data → Remove Duplicates |
| Split one column | Data → Text to Columns (e.g. "Dhaka - North" → two columns) |
| Find bad values | Ctrl + F → Find & Replace, Filter + sort to spot blanks |
| Prevent future errors | Data → Data Validation (dropdown list for City, date range for Date) |

## Practice (file: day-01-exercise-messy-sales.csv)

1. Open the CSV in Excel.
2. Use TRIM + PROPER on Customer and City columns.
3. Remove the exact duplicate row.
4. Standardise Date to YYYY-MM-DD.
5. Use Data Validation: City must be Dhaka / Chattogram / Sylhet.
6. Build a quick PivotTable: Total Amount by City. Check that Dhaka appears once, not three times.

## What I learned

- Cleaning is not cosmetic — it changes the numbers a decision-maker sees.
- TRIM + PROPER + Remove Duplicates fix most beginner mess in under 5 minutes.

## What I will try next

- Day 2: Date & text formats, Find & Replace, Data Validation in depth.
