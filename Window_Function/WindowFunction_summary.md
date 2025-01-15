# Window fnction

Window function makes it possible to perform a query over a subset of database (window), while does not group the rows into a single row in the output.

# Anatomy of window function

## Overal format
```SQL
[Window function] (oeprated_column) OVER ([PARTIOTION BY | ORDER BY] Grouping_column)
```

## Window functions:
Window function are categorized into three categories:
1. Aggregation functions: `SUM(), AVG(), MIN(), MAX(), COUNT()`
2. Ranking functions: `ROW_NUMBER(), RANK(), DENSE_RANK(), PERCENT_RANK(), NTILE()`
3. Value functions: `LAG(), LEAD(), FIRST_VALUE(), LAST_VALUE(), NTH_VALUE()`


