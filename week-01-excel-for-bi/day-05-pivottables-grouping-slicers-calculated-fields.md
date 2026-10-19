# Week 1, Day 5 — PivotTables: Grouping, Slicers, Calculated Fields

**Date:** 19 Oct 2026
**Focus:** Turn the same orders table from Days 3–4 into an interactive summary — revenue by month and category, filterable by region — without writing one formula per category.

## Why PivotTables matter

Day 4 answered "revenue by category" with one `SUMIF` per category. That works for two categories, but it does not scale: every new category, region, or month needs another formula, and the summary is frozen in one layout. A PivotTable separates the question from the layout — drag a field to Rows, Columns, or Values and the summary rebuilds itself. The same table answers "revenue by month", "revenue by region and category", and "average order value by product" in seconds, with no new formulas. In BI work this is the same idea as `GROUP BY` in SQL and `groupby` in pandas: pick the dimensions, pick the measure, and let the tool aggregate.

## Key concepts

### 1. Build from a clean, Table-formatted source

- Convert the source range to an Excel Table (`Ctrl + T`) before inserting the PivotTable, and give every column a short, unique header with no blanks and no merged cells.
- The Table expands when rows are added, so a PivotTable built on it picks up new data after a Refresh — a PivotTable built on a fixed range like `A1:G21` silently ignores row 22.
- Put the PivotTable on a new sheet, not next to the source. Source and summary on the same sheet get in each other's way when either grows.

### 2. Rows, Columns, Values, Filters — the four drop zones

| Drop zone | What it does | Example here |
|---|---|---|
| Rows | One row per value of a dimension | OrderDate (grouped by Month) |
| Columns | One column per value of a dimension | Category |
| Values | The number being aggregated | Sum of Line Total |
| Filters | A single-value filter above the table | Region |

- Start with one dimension in Rows and one measure in Values. Add a second dimension to Columns only when you can still read the result — a PivotTable with three dimensions in Rows and two in Columns is a table nobody reads.
- The default aggregation for a numeric field is `SUM`; for a text field it is `COUNT`. Check the Values label — "Sum of Line Total" vs "Count of Product" — before trusting the number.
- Right-click a value → **Show Values As** for a second view without a new formula: **% of Grand Total** (each category's share), **% of Row Total** (category mix within each month), **Running Total** (cumulative revenue through the year).

### 3. Grouping — dates and numbers

- Right-click a date field in the PivotTable → **Group** → choose **Months** (and **Years** if the data spans years). Excel builds one row per month instead of one row per day, so 20 order dates become 3 month rows.
- Grouping needs real dates, not text that looks like dates — this is where Day 2 (date formats) pays off. A single text date in the source blocks grouping for the whole field.
- The same trick works for numbers: right-click Quantity → **Group** → **By 5** turns quantities into bands (1–5, 6–10, …) for questions like "do large-quantity orders behave differently?"
- Grouped fields stay grouped on Refresh. Ungroup with right-click → **Ungroup**; do not delete the grouped rows by hand.

### 4. Slicers — filters people can actually use

- **Insert → Slicer** adds a clickable button panel for a field (here: Region, Category). One click filters the PivotTable; `Ctrl + click` selects several; the clear-filter button resets it.
- A slicer beats a Filters drop-down because the current filter is visible: anyone opening the sheet can see that only Dhaka is selected, instead of discovering it from a small drop-down label.
- One slicer can drive several PivotTables: right-click the slicer → **Report Connections** and tick every PivotTable it should filter. That is how a small dashboard stays in sync — one Region click updates the monthly chart, the category table, and the KPI at once.
- For dates, use a **Timeline** instead of a slicer: it gives Month / Quarter / Year buttons and a draggable date range.

### 5. Calculated fields — and their biggest trap

A calculated field is a formula defined inside the PivotTable (PivotTable Analyze → Fields, Items & Sets → Calculated Field). Example, with a Line Total column in the source:

```excel
'Revenue incl. VAT (15%)' = 'Line Total' * 1.15
```

- A calculated field works on **aggregates**, not on rows. Excel first sums each field, then applies the formula: `SUM(Line Total) * 1.15`. For this formula that is correct — VAT is a percentage of the total.
- The trap: `= Quantity * UnitPrice` as a calculated field does **not** give total revenue. Excel computes `SUM(Quantity) * SUM(UnitPrice)` — total quantity times total unit price — which is wrong whenever unit prices vary. The fix is a helper column in the source: `Line Total = Quantity * UnitPrice` per row, then `SUM(Line Total)` in the PivotTable.
- A second valid pattern is a ratio of two sums: `SUM(Line Total) / SUM(Quantity)` is the correct quantity-weighted average unit price, because both sums are taken first and then divided.
- Calculated fields cannot reference individual cells, cannot use `VLOOKUP` / `XLOOKUP`, and cannot return text. If the logic needs row-level conditions (`IF` per row), put it in a helper column in the source instead — exactly like the Value Band column from Day 4.
- A calculated field appears in the PivotTable Fields list for every PivotTable built on that source, and it does not update until the PivotTable is refreshed.

### 6. Refresh and layout hygiene

- A PivotTable never updates itself. After editing the source: right-click the PivotTable → **Refresh**, or PivotTable Analyze → **Refresh All**. A stale PivotTable is the most common PivotTable error — the numbers look fine and are simply old.
- For readability: right-click → **PivotTable Options** → turn off "Autofit column widths" if the layout keeps jumping, and use Design → **Report Layout → Show in Tabular Form** plus **Repeat All Item Labels** when the table will be copied elsewhere.
- Sort by the Values column (right-click a value → **Sort → Largest to Smallest**) so the biggest category or month is on top, instead of alphabetical order hiding the story.

| Need | Use |
|---|---|
| Summary by a dimension, rebuilt fast | PivotTable (Rows + Values) |
| One row per month instead of per day | Right-click date → Group → Months |
| Visible, clickable filter | Slicer (or Timeline for dates) |
| One filter driving several tables | Slicer → Report Connections |
| Formula on totals (e.g. VAT on total) | Calculated field |
| Formula that needs per-row logic | Helper column in the source, then SUM it |
| Numbers after source edits | Refresh / Refresh All |

## Practice (file: day-05-exercise-pivot-sales.csv)

1. Open the CSV in Excel. Convert it to a Table (`Ctrl + T`), then add a Line Total helper column: `=Quantity * UnitPrice` (Quantity in `F`, UnitPrice in `G` — adjust to your layout).
2. Insert a PivotTable on a new sheet. Put OrderDate in Rows, Category in Columns, and Sum of Line Total in Values. Group OrderDate by Months. Expected: Jan — Hardware 1210.00, Accessories 262.50 (total 1472.50); Feb — Hardware 1030.00, Accessories 500.00 (total 1530.00); Mar — Hardware 2780.00, Accessories 600.00 (total 3380.00); grand total 6382.50.
3. Add a second PivotTable from the same source: Region in Rows, Sum of Line Total in Values. Expected: Dhaka 3855.00, Chattogram 917.50, Sylhet 1610.00. Sort largest to smallest.
4. Add a Region slicer to the first PivotTable and click Dhaka. Expected: Dhaka + Hardware 3450.00 (check: the month totals should now sum to Dhaka's 3855.00). Clear the slicer to restore the full total.
5. Connect the slicer to both PivotTables via Report Connections, then filter to Chattogram and confirm both tables change together.
6. Add a calculated field `Revenue incl. VAT (15%)` as `'Line Total' * 1.15`. Check March: 3380.00 × 1.15 = 3887.00. Then try the trap on purpose in a copy: a calculated field `Quantity * UnitPrice` — confirm its grand total is NOT 6382.50, and note why (it multiplies the sums, not the rows).
7. Use Show Values As → % of Grand Total on the Region table: expected Dhaka ≈ 60.40%, Chattogram ≈ 14.38%, Sylhet ≈ 25.23% (sums to 100.01% after rounding). Then Refresh test: change one Quantity in the source, Refresh, and confirm the PivotTable moves; undo the change and Refresh again.

## What I learned

- A PivotTable replaces one-formula-per-category summaries with a drag-and-drop layout — same data, but the question can change without rewriting formulas.
- Grouping turns 20 daily dates into 3 month rows, and a slicer with Report Connections keeps several tables in sync with one click; both depend on clean source data (real dates, a Table, no blank headers).
- Calculated fields work on aggregates, not rows: VAT on a total is safe, but `Quantity * UnitPrice` as a calculated field silently gives the wrong answer — per-row maths belongs in a helper column.

## What I will try next

- Day 6: PivotCharts + dashboard layout — turn today's two PivotTables into one readable page with a chart, slicers, and a headline KPI.
