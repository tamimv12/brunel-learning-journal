# Week 1, Day 6 — PivotCharts + Dashboard Layout

**Date:** 20 Oct 2026
**Focus:** Turn the Day 5 PivotTables into a one-page dashboard — two PivotCharts, headline KPIs, and one Region slicer driving everything — that answers a business question at a glance.

## Why dashboards matter

Day 5 built the machinery: two PivotTables that can answer any "revenue by X" question. But a PivotTable is an answer for the analyst, not for the reader. A manager opening the workbook will not scan a 3-column table to find that March revenue doubled February — they will read a column chart in two seconds. A dashboard is the same summary data presented so the answer is visible before the question is asked: a title that states what the page shows, KPIs at the top for the headline numbers, charts for the pattern, and one slicer that keeps the whole page in sync. In BI work this is the same discipline behind a Power BI or Looker dashboard — one message per chart, the important thing at the top, filters that visibly apply to the whole page.

## Key concepts

### 1. PivotChart = a PivotTable you can see

- Select any cell inside a PivotTable → **Insert → PivotChart**. The chart is bound to the PivotTable: it draws whatever the table currently shows.
- Filtering the PivotChart's field buttons filters the underlying PivotTable as well, and vice versa — they are two views of the same summary, not two copies. This is convenient (one slicer drives both) and dangerous (a hidden filter on the chart is a hidden filter on the numbers).
- Chart field buttons (the grey buttons sitting on the chart: Axis, Legend, Values, Filters) are useful while building but noisy for readers. Right-click one → **Hide All Field Buttons on Chart** before calling the dashboard done. The filtering now lives in the slicer panel where it belongs.

### 2. Choosing the chart for the message

| Question | Chart | Why |
|---|---|---|
| How did monthly revenue change over time? | Clustered **column** | Time on the horizontal axis, height shows the size; the March jump is obvious |
| Which region earns the most? | **Bar** (horizontal) | Long category labels read easily; length is easy to rank by eye |
| What's the mix by category? | **Donut or pie** | Only for 2–4 slices; shows shares, not trends |
| Revenue vs cumulative revenue? | **Combo**: columns for monthly revenue + a line on a secondary axis for running total | One chart shows both the flow and the build-up |

- Change the type without rebuilding the pivot: right-click the chart → **Change Chart Type**. The PivotTable underneath does not care which chart type sits on top.
- A PivotChart can only show what its PivotTable shows. Need % of Grand Total on the chart? Set **Show Values As** on the PivotTable first, and the chart follows.
- Never plot a measure and its percentage of total as two bars on the same axis — 6382.50 and 100% side by side make both unreadable. Put the percentage on a secondary axis, or don't plot it.

### 3. KPIs at the top

- A dashboard's top row answers "so how are we doing?" before anyone scrolls. Typical for this sales file: **Total revenue** (6382.50), **Orders** (20), **Average order value** (319.13).
- Build each KPI as one small cell, not a table: a label in small text and the number large. `=SUM(LineTotalRange)` for total, `=COUNTA(OrderIDRange)` for orders, `=AVERAGE(order totals)` for AOV. Format numbers properly — a KPI that reads `6382.5` instead of `£6,382.50` (or `6,382.50`) looks unfinished.
- KPIs should respond to the slicer. A hard-coded `=SUM` on the source ignores the Region slicer and lies whenever a filter is active. The honest way: point the KPI at the PivotTable's grand total cell (or a `GETPIVOTDATA` reference), so "Dhaka selected → KPI shows 3855.00" instead of the full 6382.50.

### 4. Layout: a page, not a pile of charts

- **One screen, no scrolling.** If the dashboard needs a scrollbar, it is two dashboards. Move detail tables to a "Data" sheet and keep the dashboard to title, KPIs, 2–3 charts, slicers.
- **Reading order is the hierarchy:** title and date at the top, KPIs below it, charts in the middle, slicers in a strip on the left or right. Readers start top-left; put the most important number where their eyes land first.
- **Grid and breathing room:** align chart edges, give every element the same padding, don't let a chart touch the sheet edge. A dashboard where nothing lines up looks untrustworthy even when the numbers are right.
- **Colour with restraint:** one colour for the data, grey for everything else, a second colour only to highlight the point (e.g. March's bar in a darker shade). Excel's default multi-colour bar chart screams; a calm one-colour chart with one highlight gets read.
- **Title that says something:** "Sales dashboard Q1 2026" is a label, not a title. "Revenue doubled in March — Dhaka drove 60% of Q1" tells the reader the answer before they look at the charts. Dates and data source go in a subtitle so nobody asks "is this current?"

### 5. The slicer strip — the dashboard's steering wheel

- From Day 5: one Region slicer with **Report Connections** to every PivotTable keeps the charts, the category table, and the KPIs in sync. Click Dhaka and the monthly chart, the region bar chart, and all three KPIs move together.
- Put all slicers in one visible strip and label them plainly ("Region"). A slicer hidden behind a chart is a filter nobody knows is active — the same invisible-filter problem slicers were meant to solve.
- For a date range on a dashboard, use a **Timeline** instead of a date slicer: Month buttons with a draggable window beat typing dates into a filter box.

### 6. Before sharing: the honesty checks

- **Refresh.** PivotTables never update themselves — a dashboard built yesterday shows yesterday's numbers even after the source grew. Refresh All, then check one number against the source before sharing.
- **Click every slicer option** and confirm the KPIs, charts, and tables all move together. A slicer that filters the chart but not the KPI is a live mistake.
- **Clear all filters** before sending, so the recipient lands on the full picture, not on "Dhaka only" from your last test. Better: leave the visible selection obvious either way.
- **Test at 100% zoom and printed.** A dashboard that fits on your monitor can break on someone else's — View → Page Break Preview or a quick print-to-PDF catches overflow.

| Need | Use |
|---|---|
| Turn a PivotTable into a chart | Insert → PivotChart on the PivotTable |
| Monthly trend over time | Column chart, time on horizontal axis |
| Ranking categories/regions | Horizontal bar chart |
| Monthly revenue + cumulative in one chart | Combo chart with secondary axis for the running total |
| Headline numbers at the top | KPI cells, formatted, pointing at PivotTable totals |
| One filter driving the whole page | Slicer + Report Connections to every PivotTable |
| Clean chart for readers | Hide All Field Buttons on Chart; one colour + one highlight |
| Numbers after source edits | Refresh All, then re-check one number |

## Practice (file: day-06-exercise-dashboard-sales.csv)

The exercise data is the same 20-order Q1 sales file from Days 3–5 (already in the repo as `day-05-exercise-pivot-sales.csv`; this copy is here so Day 6 stands alone).

1. Open the CSV in Excel, convert to a Table (`Ctrl + T`), and add the Line Total helper column (`=Quantity * UnitPrice`).
2. Build the Day 5 PivotTables again: (a) OrderDate (grouped by Month) in Rows, Sum of Line Total in Values; (b) Region in Rows, Sum of Line Total in Values. New dashboard sheet, titled "Q1 2026 Sales Dashboard" with the subtitle "Source: orders export, 20 orders, Jan–Mar 2026".
3. Add a PivotChart on table (a): clustered **column** chart of monthly revenue. Expected bars: Jan 1472.50, Feb 1530.00, Mar 3380.00. Hide the chart field buttons. Give it a title that states the finding, e.g. "Revenue more than doubled in March".
4. Add a PivotChart on table (b): **bar** chart of revenue by region. Expected: Dhaka 3855.00, Chattogram 917.50, Sylhet 1610.00. One colour for all bars.
5. Add three KPI cells at the top: Total revenue = 6382.50, Orders = 20, Average order value = 319.13. Format with two decimals. Point them at the PivotTable grand totals (not at the raw source range) so they respond to filtering.
6. Insert a Region **slicer**, put it in a strip beside the charts, and connect it to both PivotTables via Report Connections. Click Dhaka and verify the whole page moves: monthly chart shows Jan 1210.00 / Feb 360.00 / Mar 2285.00, KPIs read 3855.00 / 8 / 481.88 (average order value = 3855 ÷ 8). Clear the slicer and confirm the full totals return.
7. Bonus: add a **combo chart** of monthly revenue (columns) plus running total (line, secondary axis). Set Show Values As → Running Total on a copy of the Values field. Expected line points: Jan 1472.50, Feb 3002.50, Mar 6382.50.
8. Run the honesty checks: Refresh All, click each region option, confirm charts + KPIs + tables all agree, clear filters, and save with the full view active.

## What I learned

- A PivotChart and its PivotTable are two views of the same summary — filtering one filters the other, so one slicer with Report Connections drives the whole dashboard.
- Dashboard layout is a reading order: title with a finding, KPIs at the top, 2–3 charts in the middle, slicers in one strip. One message per chart, one calm colour plus a highlight.
- KPIs must reference the PivotTable totals, not hard-coded sums on the source — otherwise the KPI lies whenever a slicer is active.

## What I will try next

- Day 7: Weekly mini-project — clean + summarise a messy sales file end-to-end using everything from Days 1–6: cleaning, lookups, conditional logic, PivotTables, and a one-page dashboard.
