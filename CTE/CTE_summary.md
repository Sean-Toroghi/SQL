# Common talbe expression (CTE)

CTE provides a drived table that can be used as an input table to the main query. It is not saved in memory, or added to the database. It helps to make the code more readable and simplifies the code. The main difference between CTE and subquery is that CTE is not a nested query and via labeling it can be refered to by its labeled name.

__Anatomy of CTE__
```sql

WITH labeled_name AS (
  A_query
  )
Main_Query
```
