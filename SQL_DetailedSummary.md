<h1> SQL detail summary reference</h1>

<a name = 'up'>Table of contents</a>
- [SQL logical process order in SQL](#logicorder)
- [With-clause (CTE)](#CTE)
- [Windows fucntion](#window)
- [Retrive/analyze data](#rad)
- [Date-Time manipulation](#date)

---

# <a name = 'logicorder'>Logical process order in SQL</a>
Every SQL query is processed in the following order:
1. `FROM` clause to get source data (and evaluated based on the condition provided with `ON` and type of join). The data then are put into a single data source for the next processing task.
2. `WHERE` clause to fiter rows. At this step, each row is evaluated according the pradicate and filtered rows are passed for the next processing task.
3. `GROUP BY` clause changes structure of dataset from individual rows to row groups based on the grouping expresion. 
4. `HAVING` clause employs its logical pradicates to filter the row groups. 
5. `SELECT` clause recieves filtered rows and filtered row groups. `SELECT` evaluates each expression for every row or row group. The result is a dataset that is sent to the next precessing step.
6. `ORDER BY` clause sorts the dataset and transforms it to a cursor.
7. `OFFSET FETCH` (`LIMIT FETCH`) processes the cursor and slice it according to the provided information.


---
# <a name = 'CTE'>With-clause (Common table expression - CTE)</a>
In a general form, we have two types of CTE: hierarchical CTE, and recurisve CTE. CTE could be used in the following statements:
- SELECT
- INSERT
- UPDATE
- DELETE

__Hierarchical CTE__ (or CTE) is a temporary table created by a SELECT statement, which can then be used in the main SELECT statement. Main application of the CTE is to make the statement more readable and clear. In many cases we can employ a subquery or CTE, but the use of CTE is prefereable. 

STRUCTURE:
```sql
WITH name_of_CTE AS (
  SELECT ...
)

SELECT ...
FROM name_of_CTE
```

__Recursive CTE__: CTE could be used in its simple form as a temporary table, or could be use in recusrive fashion to enable SQL to iterative processing and handle hierarchical data. Recursive CTE consists of following two parts, in which at the end the output of the two parts are combined via `UNION ALL`:
1. base case (anchor)
2. recursive part (recurisve)

STRUCTURE:
```sql
WITH name_of_CTE(
  --- Anchor part
  SELECT...
  
  UNION ALL

  --- Recursive part
  SELECT ...
  )
SELECT ...
FROM name_of_CTE
```


