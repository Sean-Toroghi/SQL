<h1> SQL quick reference</h1>

<a name = 'up'>Table of contents</a>
- [SQL logical process order in SQL](#logicorder)
- [Windows fucntion](#window)
- [Retrive/analyze data](#rad)








--- 

<br>


# <a name = 'logicorder'>Logical process order in SQL</a>
Every SQL query is processed in the following order:
1. `FROM` clause to get source data (and evaluated based on the condition provided with `ON` and type of join). The data then are put into a single data source for the next processing task.
2. `WHERE` clause to fiter rows. At this step, each row is evaluated according the pradicate and filtered rows are passed for the next processing task.
3. `GROUP BY` clause changes structure of dataset from individual rows to row groups based on the grouping expresion. 
4. `HAVING` clause employs its logical pradicates to filter the row groups. 
5. `SELECT` clause recieves filtered rows and filtered row groups. `SELECT` evaluates each expression for every row or row group. The result is a dataset that is sent to the next precessing step.
6. `ORDER BY` clause sorts the dataset and transforms it to a cursor.
7. `OFFSET FETCH` (`LIMIT FETCH`) processes the cursor and slice it according to the provided information. 

# <a name = 'window'>Window function</a>

Window function general format:
```sql
Function (expressions)
FILTER (WHERE predicatess)
  OVER (
        [PARTITION BY expressions]
        [ORDER BY expression [NULLS FIRST | LAST]]
        [frame type BETWEEEN frame start AND frame end]
        [EXCLUDE frame exclusion]
      )
```

__Function__

__FILTER__

Filter limits the scope that _function_ lookthrough over the provided _window_ with logical predicates. Here the FILTER performing only inside the _Window function_ and has no effect on the syntax outside the window function.

If we want to apply the function to the whole dataset, the syntax will be as follow:
```sql
Function (expressions)
  OVER ()
```

__OVER clause__

It defines the window over the dataset, for the _function_ to be applied to. 

## Window function vs sub-querry
In many cases subquery and window function can be used interchagibly. Following example shows the use of the two methods, to perform the same task.

__Example__: the goal is to add total number of product to the query. 
```sql
--- Employ subquery
SELECT
  Product_name,
  Product_price,
  Product_barcode,
  (SELECT COUNT (*) FROM product_info) AS count_all_products
FROM
  product_info
ORDER BY Product_name

---Window function
SELECT
  Product_name,
  Product_price,
  Product_barcode,
  COUNT (*)
    OVER ()
      AS count_all_products
FROM
  product_info
ORDER BY Product_name
```

While for a simple task, window function and subquery are both can be used, subquery will become complicated as the task gets more 

[UP](#up)

[UP](#up)

--- <br>

# <a name = 'rad'>Logical process order in SQL</a>



[UP](#up)
