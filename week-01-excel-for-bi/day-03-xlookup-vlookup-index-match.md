# Week 1, Day 3 — XLOOKUP / VLOOKUP, INDEX + MATCH in Excel

**Date:** 17 Oct 2026
**Focus:** Pull the right price and category from a lookup table instead of typing them — one source of truth, no retyping.

## Why lookups matter

Days 1–2 cleaned and protected a single table. Real work rarely lives in one table: orders list a product code, while prices and categories live in a separate price list. Typing the price into every order row is slow and dangerous — when a price changes, some rows get updated and some do not, and nobody can tell which is right. A lookup formula fetches the value from the price list at calculation time, so a single price change flows through every order automatically. In BI work this is the same idea as a join in SQL: keep each fact in one place and bring tables together when you need them.

## Key concepts

### 1. VLOOKUP — the classic, with two traps

```excel
=VLOOKUP(lookup_value, table_array, col_index_num, [range_lookup])
```

- `range_lookup` should almost always be `FALSE` (exact match). Leaving it out defaults to approximate match, which silently returns the wrong row when the lookup column is not sorted.
- It can only look to the **right**: the lookup column must be the first column of `table_array`.
- The column number is positional, so inserting a column inside the table shifts the result without any warning — `col_index_num` does not follow the data.

Example: price list in `Lookup!A2:C6` (Code, Product, Price), order code in `A2`:

```excel
=VLOOKUP(A2, Lookup!$A$2:$C$6, 3, FALSE)
```

### 2. XLOOKUP — the modern default

```excel
=XLOOKUP(lookup_value, lookup_array, return_array, [if_not_found], [match_mode], [search_mode])
```

- Defaults to an **exact match** — no approximate-match trap.
- Can look **left or right**: the lookup and return arrays are separate, so the lookup column can sit anywhere.
- The built-in `if_not_found` argument handles missing codes cleanly: `=XLOOKUP(A2, Lookup!$A$2:$A$6, Lookup!$C$2:$C$6, "Not found")`.
- It can return a whole row or column, and `search_mode` `-1` searches last-to-first (useful for "latest price" tables).
- Note: XLOOKUP needs a modern Excel (Microsoft 365 / Excel 2021+). VLOOKUP still matters for older files and shared workbooks.

### 3. INDEX + MATCH — the flexible classic

```excel
=INDEX(return_array, MATCH(lookup_value, lookup_array, 0))
```

- `MATCH(..., 0)` returns the **position** of the exact match; `INDEX` returns the value at that position in another column.
- Like XLOOKUP, it can look left, and it does not break when columns are inserted, because each part references its own range.
- Two-way lookup: one MATCH for the row, one for the column — `=INDEX(price_table, MATCH(product, code_col, 0), MATCH(month, header_row, 0))`.

| Need | Use |
|---|---|
| Quick lookup in a modern file, lookup column anywhere | `XLOOKUP` |
| Lookup in an old / shared workbook | `VLOOKUP(..., FALSE)` |
| Look left in an old workbook, or two-way lookup | `INDEX + MATCH` |
| Missing code should show a friendly message, not `#N/A` | `IFNA(XLOOKUP(...), "Not found")` or XLOOKUP's `if_not_found` |

### 4. Handling missing values

- `#N/A` means "no match found" — it is information, not just an error. Before hiding it, check whether the code is genuinely missing or mistyped (extra space, different case handling — MATCH/VLOOKUP are case-insensitive, so `p101` matches `P101`).
- Wrap only the lookup, not a whole calculation: `=IFNA(XLOOKUP(...), "Not found")` keeps real errors in the rest of the formula visible. `IFERROR` hides every error type, so prefer `IFNA` for lookups.
- Fix the cause (add the code to the price list) rather than hiding `#N/A` everywhere — a hidden gap becomes a wrong total.

## Practice (file: day-03-exercise-orders-lookup.csv)

1. Open the CSV in Excel. It has orders with a ProductCode but no price or category. Create a second sheet named `Lookup` and type in the price list from the table below.
2. In the orders sheet, add a Price column with XLOOKUP (or VLOOKUP if your Excel is older) pulling Price from `Lookup`. Lock the ranges with `$` so the formula copies down cleanly.
3. Add a Category column the same way. Order 208 uses code `P999`, which is not in the price list — make it show `Not found` instead of `#N/A`.
4. Add a Line Total column: `=Quantity * Price`. For the `Not found` row, the total will error — decide deliberately: leave the error visible until the code is fixed, or wrap only the lookup, and note why you chose that.
5. Re-do the Price column with INDEX + MATCH and check every row gives the same answer as XLOOKUP. Then change the price of `P102` in the Lookup sheet and confirm all `P102` order totals update without editing any order row.
6. Deliberately break it, then fix it: insert a column inside the VLOOKUP table range and watch the VLOOKUP column go wrong while XLOOKUP / INDEX + MATCH stay right. Undo the insert.

Lookup table for the `Lookup` sheet:

| Code | Product | Category | Price |
|---|---|---|---|
| P101 | Laptop | Hardware | 850.00 |
| P102 | Mouse | Accessories | 12.50 |
| P103 | Keyboard | Accessories | 25.00 |
| P104 | Monitor | Hardware | 180.00 |
| P105 | Headset | Accessories | 45.00 |

## What I learned

- Lookups replace retyping with a single source of truth: one price change in the Lookup sheet updates every order that uses it.
- VLOOKUP's two traps are real — approximate match by default and right-only lookup — and XLOOKUP removes both, but VLOOKUP is still the tool for older workbooks.
- `#N/A` is a signal, not noise: wrap it with IFNA only after checking whether the code is genuinely missing.

## What I will try next

- Day 4: IF, IFS, COUNTIF / SUMIF — labelling orders (e.g. high-value vs standard) and summarising totals by category without filtering by hand.
