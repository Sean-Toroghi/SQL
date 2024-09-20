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


[UP](#up)

[UP](#up)

--- <br>

# <a name = 'rad'>Logical process order in SQL</a>



[UP](#up)
