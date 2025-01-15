# Window fnction

Window function makes it possible to perform a query over a subset of database (window), while does not group the rows into a single row in the output.

# Anatomy of window function
```SQL
[Window function] (oeprated_column) OVER ([PARTIOTION BY | ORDER BY] Grouping_column)
```
A window function consists of three main parts: 
