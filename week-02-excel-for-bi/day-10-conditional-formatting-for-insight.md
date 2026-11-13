# Week 2, Day 10 — Conditional Formatting for Insight

**Date:** 6 Nov 2026
**Focus:** Use conditional formatting on a clean sales table to highlight what matters — top sales, a falling month, values above average — without changing any number.

## Why conditional formatting matters

Days 5–9 built clean tables and pivots. A clean table is accurate, but a reader still has to scan every cell to find the story. Conditional formatting is the layer that points the eye: the rule watches the values, and the colour follows the data. When the numbers change — after a Day 9 Power Query refresh, for example — the highlighting updates itself, because it is a rule, not a hand-painted fill.

The discipline is the same as in Day 9: formatting is display, not data. It must never change a value, hide a value, or be the only place a conclusion lives. If the colour is removed, the table and its totals must still tell the same story.

## Key concepts

### 1. Rules, not manual fills

Home → Conditional Formatting offers ready rules: Highlight Cells Rules (Greater Than, Top 10, Above Average), Data Bars, Colour Scales, and Icon Sets. A manual fill colour on a cell is frozen — it stays even when the value changes and becomes a lie. A rule re-evaluates every time the sheet recalculates. In this exercise, every highlight below is a rule, applied to the Sales column only, so the Region and Month labels stay plain and readable.

### 2. Above Average — one rule, four cells

The exercise file has 9 sales values totalling 4,250.00, so the average is 4,250.00 ÷ 9 = 472.22. The Above Average rule highlights exactly four cells: Dhaka Jan 820, Dhaka Feb 940, Dhaka Mar 610, and Sylhet Mar 510. Everything else — Sylhet Jan 420, Sylhet Feb 380, Chattogram Jan 180, Chattogram Feb 240, Chattogram Mar 150 — stays unhighlighted. That is the check that the rule is working: if five or three cells light up, the range is wrong, not the data.

### 3. Top 3 — and why "Top 10" needs a second look

Excel's Top 10 rule defaults to the top 10 items, which in a 9-row table would highlight everything — a useless result. Set it to Top 3 instead. The top three are Dhaka Feb 940, Dhaka Jan 820, and Dhaka Mar 610 — all Dhaka. That is itself a finding: the top of the table is one region. A rule that highlights everything tells the reader nothing; narrowing the rule until it isolates a real pattern is part of the work.

### 4. Data Bars and Colour Scales — direction, not decoration

A Data Bar in the Sales column makes the same ranking visible at a glance: Dhaka Feb has the longest bar, Chattogram Mar the shortest. A two-colour scale (low = pale, high = strong) does the same job with colour instead of length. Use one or the other on the same column, not both — two encodings of the same number compete and the reader has to decide which one to trust. And never put a Data Bar or Colour Scale across a column that mixes units (sales next to quantities, for example): the longest bar would compare things that should never be compared.

### 5. A falling month is a comparison, not a colour

Dhaka falls from 940 in Feb to 610 in Mar — a drop of 330. No built-in rule spots that on its own, because the rule looks at one column of values, not at a change between two months. The honest way to show it is to add a helper column first (Change = Mar − Feb, or % change), then format that column — for example, highlight negative changes. Formatting the raw Mar cell red would suggest the value itself is bad; formatting the change says what actually happened. The same applies to the monthly totals: Jan 1,420, Feb 1,560, Mar 1,270 — Mar is the lowest month overall, down 290 from Feb. Say that in words next to the table; do not rely on colour alone to carry it.

## Practice (file: day-10-exercise-conditional-formatting.csv)

The file has 9 rows — 3 regions × Jan, Feb, Mar — already clean, so no Power Query step is needed today.

1. Load the file as an Excel Table. Confirm the totals before formatting anything: grand total 4,250.00; by region Dhaka 2,370.00, Sylhet 1,310.00, Chattogram 570.00; by month Jan 1,420.00, Feb 1,560.00, Mar 1,270.00. If any total is off, fix the data first — formatting a wrong table only highlights the wrong answer faster.
2. Select the Sales column only. Apply Highlight Cells Rules → Above Average. Confirm exactly 4 cells highlight: Dhaka Jan 820, Dhaka Feb 940, Dhaka Mar 610, Sylhet Mar 510.
3. On the same column, apply Top 10 Items — but change 10 to 3. Confirm the top three are Dhaka Feb 940, Dhaka Jan 820, Dhaka Mar 610. (If all 9 highlight, the rule is still set to 10.)
4. Add Data Bars to the Sales column. Confirm Dhaka Feb has the longest bar and Chattogram Mar (150, the lowest value) the shortest. Remove the Colour Scale if you also tried one — keep one encoding per column.
5. Add a Change column for Dhaka only, or for all three regions (Mar − Feb): Dhaka −330, Sylhet +130, Chattogram −90. Highlight the negative changes. Note the finding in words: Dhaka drives the March fall; Sylhet is the only region that rose in March.
6. Print-preview the sheet. If the colours make the numbers harder to read, the formatting has failed its job — lighten the fill or keep only the Above Average rule and drop the rest.

## What I learned

- Conditional formatting is a rule that follows the data: after a refresh, the highlighting updates itself, while a hand-painted fill would stay frozen and become wrong.
- A rule has to be checked against a counted answer — Above Average must highlight exactly 4 cells here, Top 3 exactly Dhaka Feb, Jan, and Mar — otherwise the range, not the data, is at fault.
- One encoding per column: a Data Bar or a Colour Scale, not both, and never across mixed units.
- A change between months needs a helper column first; colouring a raw value cannot say "this fell", only "this is low".

## What I will try next

- Carry this highlighted table back into a Day 5-style pivot: check whether the same story (Dhaka on top, March down, Sylhet rising) still shows when the data is summarised by region and month, and whether the formatting rules survive when the pivot refreshes.
