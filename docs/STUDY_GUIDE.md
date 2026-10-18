# Line-by-line review plan

Use this guide after completing the portfolio, as a preparation for an independent project.

1. **01–02 Orders:** explain CSV headers, string preservation, DataFrame transformations versus saved tables, timestamp parsing, null comparisons, composite conditions, and quality flags.
2. **03–04 Items:** explain item-level grain, decimal precision, key uniqueness, and left anti joins. Identify which checks print and which raise exceptions.
3. **05–06 Products:** explain renamed source fields, loops for repeated casts, null preservation, and category defaults in Gold.
4. **07–08 Customers:** explain customer_id versus customer_unique_id, ZIP prefixes as text, trim/lower/upper, conditional padding, and state validation.
5. **09–10 Payments:** explain payment sequence versus installments, zero-value records, Boolean flags, and null aggregation.
6. **11 Order summary:** draw an example with two item rows and three payment rows. Show how a direct join would create six rows and why preaggregation prevents inflation. Explain left joins and separate monetary metrics.
7. **12 Metrics:** explain grouping grain, denominators, duplicate customers across months, overlapping category order counts, calendar-date lateness, and averages with null exclusions.
8. **13 Validation:** explain what each assertion proves and what it cannot prove. Distinguish count matching from content equality.
9. **Job:** explain task dependencies, failure propagation, snapshot overwrite, concurrency, and why a multi-table refresh is not atomic.

For each code cell: describe input grain, intended transformation, output grain, null behavior, and validation before reading the syntax aloud. Then reproduce a small variant in a separate practice notebook.

For the independent project, choose a new source and define its keys, grains, quality rules, and business metrics before adapting any code. Avoid copying fixed Olist counts into a new dataset.
