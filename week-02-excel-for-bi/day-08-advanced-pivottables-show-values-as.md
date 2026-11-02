# Week 2, Day 8 — Advanced PivotTables: Show Values As, Multiple Measures, and GETPIVOTDATA

**Date:** 1 Nov 2026
**Focus:** Go beyond "Sum of Line Total" from Day 5. The same PivotTable can answer a different business question just by changing how a value is shown — share of total, running total, change from the previous month — and a dashboard KPI can read a PivotTable cell safely with GETPIVOTDATA instead of a fragile cell reference.

## Why advanced pivots matter

Day 5 answered "how much?" — revenue by month, by region, by category. That is the first question, not the only one. A manager's next questions are comparative: "Is March really half the quarter, or does it just look big?", "Did February actually grow, or just hold steady?", "Which region carried each month?" A plain Sum cannot answer those without mental arithmetic, and mental arithmetic is where a dashboard starts to mislead. `Show Values As` answers them from the same PivotTable, without touching the source data and without a second helper column. The numbers below all come from the same 20-order Q1 sales file used on Days 3–6, so every figure here can be checked against a Day 5 pivot.

## Key concepts

### 1. Show Values As — same numbers, different question

Right-click a Values field → **Show Values As**. The underlying values do not change; only the display calculation changes. Adding a second copy of the same field lets one PivotTable show both the amount and the share side by side: drag "Sum of Line Total" into Values twice, leave the first as No Calculation, set the second to the Show Values As option you need.

| Show Values As | Question it answers | Verified example from this file |
|---|---|---|
| No Calculation | How much? | Mar revenue = 3,380.00 |
| % of Grand Total | How big a share of everything? | Mar = 52.96% of Q1 (3,380.00 ÷ 6,382.50); Dhaka = 60.40% of Q1 |
| % of Row Total | Within this row, how is it split? | In Jan, Dhaka = 82.2% of the month (1,210.00 ÷ 1,472.50) |
| % of Column Total | Within this column, how is it split? | In the Dhaka column, Mar = 59.3% of Dhaka's quarter (2,285.00 ÷ 3,855.00) |
| Running Total In | How does it build up over time? | Jan 1,472.50 → Feb 3,002.50 → Mar 6,382.50 |
| Difference From (previous month) | How much did it move? | Mar − Feb = +1,850.00; Feb − Jan = +57.50 |
| % Difference From (previous month) | How fast did it move? | Mar vs Feb = +120.9%; Feb vs Jan = +3.9% |

The February example is why this matters. A Sum pivot says February (1,530.00) barely moved from January (1,472.50) — a +3.9% change. A Region × Month pivot with % of Row Total says something the Sum hides: February's mix flipped completely. Dhaka fell from 82.2% of January to 23.5% of February, while Sylhet jumped from 5.1% to 73.2%, driven by a single Laptop order (850.00). Same quarter, same totals, different story — and the second story is the one a manager needs before asking why Dhaka slowed down.

### 2. Multiple value fields in one PivotTable

A PivotTable does not have to show one measure. Drag the same field into Values three times and set each differently: **Sum** of Line Total, **Count** of OrderID, **Average** of Line Total. By region, that gives:

| Region | Orders (Count) | Revenue (Sum) | Average order value |
|---|---|---|---|
| Dhaka | 8 | 3,855.00 | 481.88 |
| Chattogram | 6 | 917.50 | 152.92 |
| Sylhet | 6 | 1,610.00 | 268.33 |
| **Grand Total** | **20** | **6,382.50** | **319.13** |

Read Sum and Average together, never alone. Dhaka's revenue lead is partly volume (8 orders) and partly order size (481.88 average, against 152.92 in Chattogram). A region can look small on revenue while being healthy on average order value — Chattogram's problem here is order size and count, not just total. The Day 6 warning still applies: do not chart a Sum and its percentage on the same axis. Two value fields in a table are fine; two incompatible scales in one chart are not.

### 3. Sorting, Top 10, and grouping — in that order

- **Sort by value, not by label.** A Region pivot sorted A–Z (Chattogram, Dhaka, Sylhet) hides the ranking. Right-click a value → Sort → Sort Largest to Smallest gives Dhaka, Sylhet, Chattogram — the ranking a reader actually wants.
- **Top 10 filter is a Value Filter, not a row limit.** Click the Row Labels drop-down → Value Filters → Top 10, and set it to Top 2 by Sum of Line Total to show Dhaka and Sylhet only. It filters by the value, so it updates if the data changes — unlike deleting rows by hand.
- **Grouping dates is the same tool as Day 5, used further.** A date field groups by Month by default in the exercise below, but the Grouping dialog also offers Quarters and Years. With only Q1 data, Quarter grouping gives a single Q1 bucket (6,382.50) — correct, but useless. Grouping only pays off when the range spans more than one bucket, which is a good check before adding a grouping level at all.

### 4. Filters change the denominator — watch % of Grand Total

This is the trap in this whole topic. % of Grand Total is a percentage of the PivotTable's *visible* grand total, not of the original file. Filter the pivot to Dhaka only and Dhaka reads 100% — true for the filtered pivot, false for the quarter. The same applies to a slicer: a Region slicer set to Dhaka makes every % of Grand Total on that page read against 3,855.00, not 6,382.50. Two defences: keep one unfiltered pivot (or a KPI cell, see §5) showing the full-quarter total on the same page, and always label a filtered percentage ("share of Dhaka", not "share of Q1"). A percentage without its denominator stated is a claim nobody can check.

### 5. GETPIVOTDATA — the KPI cell that survives a moved pivot

Day 6 said KPIs should point at PivotTable totals, not at raw source sums, so they respond to slicers. The safe way to do that is GETPIVOTDATA, not a plain `=C7` reference. Type `=` and click a PivotTable value cell, and Excel writes something like:

```
=GETPIVOTDATA("Line Total", $A$3, "Region", "Dhaka")
```

That formula asks for the Line Total for Dhaka by name, not by address. Insert a row above the pivot, or sort the pivot so Dhaka moves down a row, and the formula still returns 3,855.00 — a plain `=C7` would now point at the wrong region. Two things to know: GETPIVOTDATA breaks (returns `#REF!`) if the field it names is renamed or removed from the pivot, so do not rename pivot fields after KPIs depend on them; and if you ever want a plain reference instead, turn it off under PivotTable Analyze → Options → uncheck "Generate GetPivotData" — then `=` plus a click gives a normal cell reference. For dashboard KPIs, leave it on.

| Need | Use |
|---|---|
| Share of the whole quarter | Show Values As → % of Grand Total |
| Share within a month or region | Show Values As → % of Row / Column Total |
| Build-up over time | Show Values As → Running Total In |
| Month-on-month move, in money or percent | Show Values As → Difference / % Difference From (previous item) |
| Amount + count + average in one table | Same field in Values three times, each summarised differently |
| Ranked list, not alphabetical | Sort Largest to Smallest; Top 10 Value Filter for the top N |
| KPI that survives a moved or sorted pivot | GETPIVOTDATA, not a plain cell reference |
| A filtered percentage that stays honest | Label its denominator; keep the full total visible on the page |

## Practice (file: day-08-exercise-advanced-pivots.csv)

The exercise data is the same 20-order Q1 sales file from Days 3–6 (already in the repo as `day-05-exercise-pivot-sales.csv`; this copy is here so Day 8 stands alone).

1. Open the CSV in Excel, convert to a Table (`Ctrl + T`), and add the Line Total helper column (`=Quantity * UnitPrice`). Total must be 6,382.50 before you pivot anything.
2. Build a PivotTable with OrderDate (grouped by Month) in Rows and Sum of Line Total in Values. Add a second copy of Sum of Line Total and set it to % of Grand Total. Expected: Jan 1,472.50 / 23.07%, Feb 1,530.00 / 23.97%, Mar 3,380.00 / 52.96%.
3. Add a third copy of the field, set to Running Total In (base field: OrderDate). Expected: 1,472.50, 3,002.50, 6,382.50. Add a fourth copy, set to % Difference From previous month. Expected: Feb +3.9%, Mar +120.9%.
4. Build a Region × Month pivot (Region in Rows, Month in Columns, Sum of Line Total). Set a copy of the value to % of Row Total and find the February flip: Jan — Dhaka 82.2%, Chattogram 12.7%, Sylhet 5.1%; Feb — Dhaka 23.5%, Chattogram 3.3%, Sylhet 73.2%; Mar — Dhaka 67.6%, Chattogram 20.1%, Sylhet 12.3%.
5. Build a Region pivot with three value fields: Count of OrderID, Sum of Line Total, Average of Line Total. Expected: Dhaka 8 / 3,855.00 / 481.88; Chattogram 6 / 917.50 / 152.92; Sylhet 6 / 1,610.00 / 268.33. Sort Largest to Smallest by Sum, then apply a Top 2 Value Filter and confirm only Dhaka and Sylhet remain.
6. In a KPI area, type `=` and click the Dhaka total in the Region pivot. Confirm Excel writes a GETPIVOTDATA formula. Sort the pivot Smallest to Largest and confirm the KPI still reads 3,855.00. Then filter the pivot to Dhaka only and note that a % of Grand Total field on that pivot now reads 100% — label it as "share of filtered total" if you keep it.
7. Refresh check: change one UnitPrice in the source, Refresh All, and confirm all four Show Values As columns update. Change it back and refresh again.

## What I learned

- Show Values As turns one PivotTable into several answers — share, running total, and month-on-month change — without touching the source data or adding helper columns.
- The February flip (Dhaka 82.2% of January, Sylhet 73.2% of February) is invisible in a Sum pivot and obvious in a % of Row Total pivot; the calculation you choose decides which story the reader sees.
- A filtered percentage is a percentage of the visible total, not the original file, so a filtered % of Grand Total must be labelled with its denominator, and dashboard KPIs should use GETPIVOTDATA so they keep pointing at the right value when the pivot moves or re-sorts.

## What I will try next

- Day 9: Power Query basics — import and clean a messy file in Power Query instead of with formulas, and compare the effort and repeatability against the Day 1–2 formula approach.
