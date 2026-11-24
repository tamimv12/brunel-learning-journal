# Week 2, Day 11 — Mini Dashboard: One-Page Sales Dashboard

**Date:** 14 Nov 2026
**Focus:** Build a one-page sales dashboard from the Week 2 work — PivotTables as the engine, PivotCharts for the picture, one slicer to filter everything, and three KPI numbers on top — so the answer to "how are sales doing?" is visible without scrolling.

## Why a dashboard matters

Days 5–10 built the parts: clean tables, pivots, Power Query refresh, conditional formatting. A dashboard is what those parts are for. A manager does not open a spreadsheet to admire a PivotTable; they open it to answer one question fast — which region is carrying sales, which month fell, and whether the total is on track. If the answer takes more than a few seconds to find, the dashboard has failed, however correct the numbers are.

The discipline from Day 10 still applies: the dashboard shows the data, it does not become the data. Every number on the page must trace back to the table, and removing the colours or the charts must leave a summary that still tells the same story.

## Key concepts

### 1. One page, one question

A dashboard answers one question: "How are sales doing, by region and month?" Everything on the page serves that question. A chart that answers a different question — headcount, say, or last year's figures — belongs on another page. Before adding any visual, ask what decision it helps: this dashboard helps decide where to focus effort next month. Dhaka is carrying sales; Chattogram is small and falling in March; that is the decision the page must make obvious.

### 2. Build from pivots, not from the raw table

The charts do not read the raw 18-row table. They read PivotTables built from it: one pivot for sales by region and month, one for sales by category. That is what makes the dashboard live. When the table grows — a new month is added, or a figure is corrected — the pivots refresh and the charts follow. A chart drawn from hand-typed totals is frozen the moment the data changes, the same trap as the manual fill colour in Day 10.

### 3. One slicer filters everything

A slicer is the dashboard's filter. Add a Month slicer and connect it to both pivots (PivotTable Analyse → Filter Connections, or right-click the slicer → Report Connections). Clicking "Feb" then filters the region chart, the category chart, and the totals together. One slicer, every visual — that is the test. If a chart does not move when the slicer changes, it is not connected, and the reader is looking at two different answers on one page.

### 4. KPI tiles: three numbers, no decoration

The top of the page holds three big numbers, each with a short label:

| KPI | Value | How it is worked out |
| --- | --- | --- |
| Total sales (Jan–Mar) | 4,250.00 | Sum of all 18 rows |
| Best region — Dhaka | 2,370.00 (55.8% of total) | 2,370 ÷ 4,250 |
| Best month — Feb | 1,560.00 (36.7% of total) | 1,560 ÷ 4,250 |

Three is enough. A fourth or fifth tile would compete for attention and none of them would be read. Each tile is a formula pointing at the pivot, not a typed number — typed numbers go stale, formulas do not.

### 5. Three visuals, one message each

- **Column chart — sales by month, split by region.** The message: February is the peak (1,560), March falls to 1,270, and Dhaka is the tallest bar in every month. That matches Day 10 exactly — same data, same story.
- **Bar chart — sales by region.** The message: Dhaka (2,370) is nearly double Sylhet (1,310) and more than four times Chattogram (570). A horizontal bar suits a ranking; the longest bar is read first.
- **Column or bar chart — sales by category.** The message: Electronics (2,720, 64.0%) outsells Groceries (1,530, 36.0%) in every region, so the category mix is not a regional story.

One message per chart, stated in words under or above it. A chart without a one-line takeaway asks the reader to do the analyst's job.

### 6. Conditional formatting belongs on the summary, not on the pivot

It is tempting to put the Day 10 Above Average rule straight onto the pivot values. Resist it: a rule painted over a pivot's value area can behave oddly when the pivot refreshes or changes shape, and it formats a calculated cell, not the source. The safe place for the rule is the small summary table beside the dashboard (region totals, with Above Average highlighted) or the KPI tiles. Formatting the source summary keeps the rule stable; the pivot stays clean and refreshable.

### 7. Layout: title, KPIs, charts, takeaway

Top to bottom: a short title ("Sales Dashboard — Jan–Mar"), the three KPI tiles, the slicer, the three charts, and one or two lines of takeaway at the bottom. Nothing below the takeaway. Print-preview the page: if any chart is cut off or the page spills onto a second sheet, the layout is wrong, not the printer. A dashboard that needs scrolling or a second page is a report, not a dashboard.

## Practice (file: day-11-exercise-mini-dashboard.csv)

The file has 18 rows — 3 regions × 3 months × 2 categories. It is the Day 10 table split by category: the totals reconcile exactly with Day 10, so this dashboard can be checked against work already done.

1. Load the file as an Excel Table. Confirm the totals before building anything: grand total 4,250.00; by region Dhaka 2,370.00, Sylhet 1,310.00, Chattogram 570.00; by month Jan 1,420.00, Feb 1,560.00, Mar 1,270.00; by category Electronics 2,720.00, Groceries 1,530.00. If any total is off, fix the data first — a dashboard built on a wrong table only presents the wrong answer faster.
2. Build two PivotTables from the table: sales by Region × Month, and sales by Category. Check both against the totals in step 1.
3. Add a Month slicer to the Region × Month pivot, then connect it to the Category pivot as well (Report Connections). Click Feb: both pivots should show Feb only — Dhaka 940, Sylhet 380, Chattogram 240; Electronics 1,020, Groceries 540. Click Mar: Dhaka 610, Sylhet 510, Chattogram 150. If one pivot does not move, the connection is missing.
4. Create the three charts from the pivots: a column chart of sales by month split by region, a bar chart of sales by region, and a chart of sales by category. Write a one-line takeaway under each.
5. Add the three KPI tiles at the top as formulas pointing at the pivots: total 4,250.00, Dhaka 2,370.00 (55.8%), Feb 1,560.00 (36.7%). Change one value in the source table, refresh, and confirm the tiles and charts move — that is the proof the dashboard is live.
6. Print-preview on one page. If a chart is cut off, shrink it or drop the category chart before you shrink the text — the numbers must stay readable.

## What I learned

- A dashboard is judged by one test: can the reader answer the question in a few seconds? Three KPI tiles and three charts with one message each pass it; more visuals would not.
- Charts must read from PivotTables, and one slicer must filter every visual — otherwise the page shows two different answers at once.
- The Day 11 data reconciles exactly with Day 10 (same 4,250.00 total, same region and month splits), which is how I know the dashboard is built on correct numbers: the category split is new, the totals are already proven.
- Conditional formatting goes on the summary table, not painted over a pivot — rules on calculated cells misbehave when the pivot refreshes.

## What I will try next

- Start Week 3 (SQL): the same sales questions — total by region, best month, category split — answered with SELECT, WHERE, and GROUP BY instead of a pivot, and compare which is faster for each question.
