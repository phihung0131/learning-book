### **1. Subqueries: Correlated vs. Non-correlated**

**Theory**

A **Subquery (or inner query)** is a `SELECT` statement nested inside another SQL statement (`SELECT`, `INSERT`, `UPDATE`, or `DELETE`, or even inside another subquery).

*   **Analogy:** Imagine your boss asks you: "Find me the employees whose salary is higher than the company's average salary."
    *   To answer this, you must take two steps:
        1.  Calculate the average salary of the company. (This is the subquery)
        2.  Get the list of employees with a salary greater than the number you just calculated. (This is the outer query)
    *   A subquery allows you to perform both steps in a single statement.

**Non-Correlated Subquery**

*   **Definition:** An **independent** subquery. It can be run on its own without any information from the outer query. The database executes this subquery **only once**, gets its result, and then uses that result for the outer query.
*   **Example (the average salary problem):**
    ```sql
    SELECT employee_name, salary
    FROM employees
    WHERE salary > (SELECT AVG(salary) FROM employees); -- This subquery can run by itself
    ```
    *   **How it works:**
        1.  The database runs `(SELECT AVG(salary) FROM employees)` first. Let's assume the result is `55000`.
        2.  The outer query becomes `SELECT ... WHERE salary > 55000;` and is then executed.

**Correlated Subquery**

*   **Definition:** A **dependent** subquery. It uses values from the row that the outer query is currently processing. Therefore, this subquery is **executed repeatedly, once for each row** of the outer query.
*   **Analogy:** Your boss now asks: "Find me the employees who have the highest salary within *their own department*."
    *   For each employee, you have to:
        1.  See which department that employee belongs to.
        2.  Find the highest salary in that specific department. (This is the correlated subquery)
        3.  Compare the current employee's salary with the number you just found.

*   **Example:**
    ```sql
    SELECT employee_name, salary, department_id
    FROM employees AS e1
    WHERE salary = (
        SELECT MAX(salary)
        FROM employees AS e2
        WHERE e2.department_id = e1.department_id -- Depends on e1 from the outer query
    );
    ```
    *   **How it works:**
        1.  The outer query processes the first row, for example, employee Ann in department `D1`.
        2.  The subquery is run with `WHERE e2.department_id = 'D1'`, finding the max salary for department D1.
        3.  Ann's salary is compared to that max salary.
        4.  The outer query processes the next row, employee Ben in department `D2`.
        5.  The subquery is run *again* with `WHERE e2.department_id = 'D2'`, finding the max salary for department D2.
        6.  ...and so on, until the end of the table.

**Where Subqueries Can Be Used:**
*   In the `SELECT` clause: The subquery must return a single value (a scalar subquery).
*   In the `FROM` clause: The subquery returns a temporary table (a derived table).
*   In the `WHERE` clause: The most common use, often with operators like `IN`, `NOT IN`, `ANY`, `ALL`, and `EXISTS`.

---

**SQL Examples**

**Non-Correlated:**
```sql
-- Find customers who have never placed an order
SELECT customer_name
FROM customers
WHERE customer_id NOT IN (SELECT DISTINCT customer_id FROM orders);
```

**Correlated (with `EXISTS`):**
`EXISTS` is an efficient operator for checking existence. It returns `TRUE` if the subquery returns at least one row.
```sql
-- Find all departments that have at least one employee
SELECT d.department_name
FROM departments d
WHERE EXISTS (
    SELECT 1
    FROM employees e
    WHERE e.department_id = d.department_id -- Correlated
);
```
*   **Explanation:** For each department `d`, the database runs the subquery to see if any employee `e` matches the `department_id`. As soon as one match is found, `EXISTS` returns `TRUE` and the database stops the subquery immediately, making it very efficient.

---

**Mini-Exercise**

Using an `employees` table (id, name, department_id, salary), write a subquery to find the names of employees working in the department named 'Sales'. Assume you also have a `departments` table (id, name).

**Solution**

```sql
SELECT name
FROM employees
WHERE department_id = (
    SELECT id
    FROM departments
    WHERE name = 'Sales'
);
```
*   **Classification:** This is a **non-correlated subquery**. The subquery `(SELECT id FROM departments WHERE name = 'Sales')` is run once to get the ID for the 'Sales' department, and its result is then used to filter the `employees` table.

---

**Interview Questions**

*   **Question:** "What is the main difference between a Correlated and a Non-correlated Subquery? Which one is generally faster?"
    *   **Answer:** A non-correlated subquery is an independent query that is executed once before the outer query. A correlated subquery depends on the outer query and is executed repeatedly, once for each row processed by the outer query. Therefore, a **non-correlated subquery is generally significantly faster**. Correlated subqueries can cause severe performance issues on large tables because they behave like a nested loop.

*   **Question:** "When would you use a `JOIN` instead of a `Subquery`?"
    *   **Answer:** In many cases, a subquery (especially non-correlated) can be rewritten as a `JOIN`, and vice versa. Most modern query optimizers are smart enough to transform them and pick the best execution plan. However, by convention:
        *   **`JOIN`s are often preferred** because they are generally more readable and explicitly state the relationship between tables.
        *   **Subqueries** are very useful when you need to compute an aggregate value beforehand (like `AVG`, `MAX`) for comparison, or when the logic is complex and separating it into a subquery makes the statement easier to understand.
        *   `NOT IN` with a subquery can behave unexpectedly if the subquery returns `NULL` values. In these cases, `LEFT JOIN ... WHERE ... IS NULL` or `NOT EXISTS` are safer and more performant alternatives.

*   **Question:** "`EXISTS` vs `IN`: Which do you choose and why?"
    *   **Answer:**
        *   `EXISTS` only checks for the existence of a matching row. As soon as it finds one, it stops and returns `TRUE`. It doesn't care how many rows match.
        *   `IN` compares a value against a list. It must collect all results from the subquery first, and then perform the comparison.
        *   **General Rule:** `EXISTS` is often more performant than `IN` when the subquery returns a very large result set, because `EXISTS` can "short-circuit" or exit early. `IN` can be more performant if the subquery's result set is very small.

---

**Best Practices & Common Mistakes**

*   **Best Practice:** Always try to rewrite a correlated subquery as a `JOIN` if possible. The performance will usually be much better.
    *   For example, instead of `WHERE salary = (SELECT MAX(salary) FROM ... WHERE ...)` you might use a Window Function (covered next) or a `JOIN`.
*   **Best Practice:** When using a subquery that returns a table (e.g., `FROM (SELECT ...)`), always give it an alias. Example: `... FROM (SELECT ...) AS derived_table;`.
*   **Common Mistake:** Writing a subquery in the `SELECT` clause that can return more than one row. This will cause a runtime error. A subquery in this position must always be scalar (return 1 row, 1 column).
*   **Common Mistake:** Using `NOT IN` with a subquery that might contain `NULL` values.
    *   For example, `WHERE id NOT IN (1, 2, NULL)`. This will never return any rows, because in SQL, `id = NULL` evaluates to `UNKNOWN`, and therefore `id != NULL` also evaluates to `UNKNOWN`.
    *   Use `NOT EXISTS` or `LEFT JOIN` instead, as they handle `NULL`s safely.

---

### **2. Window Functions: `ROW_NUMBER()`, `RANK()`, `DENSE_RANK()`, `LAG()`, `LEAD()`**

**Theory**

**Window Functions** are one of the most powerful features of modern SQL. They allow you to perform calculations across a set of rows (a "window") that are related to the current row, but unlike `GROUP BY`, they **do not collapse the rows**. The result of the window function is returned as a new column for each row.

*   **Analogy:** Imagine you have a list of student exam scores, sorted from highest to lowest.
    *   `GROUP BY` is like calculating the "average score of the entire class" -> this gives you only one number.
    *   A **Window Function** is like walking down the list and doing the following:
        *   "Assign a serial number from 1 to the end" -> `ROW_NUMBER()`
        *   "Assign a rank, where students with the same score get the same rank, and the next rank is skipped" -> `RANK()`
        *   "Compare this student's score to the score of the student *immediately above* them on the list" -> `LAG()`

**General Syntax:**
`FUNCTION_NAME() OVER ( [PARTITION BY ...] [ORDER BY ...] )`

*   `FUNCTION_NAME()`: The name of the window function (`ROW_NUMBER`, `SUM`, `AVG`...).
*   `OVER()`: The keyword that signals a window function.
*   `PARTITION BY column_name`: Divides the rows into separate "partitions" (groups). The window function is reset and calculated independently for each partition. It's like `GROUP BY` but without collapsing rows.
*   `ORDER BY column_name`: Sorts the rows within each partition. This is crucial for ranking and positional functions.

**Common Functions:**

1.  **Ranking Functions:**
    *   `ROW_NUMBER()`: Assigns a unique, sequential integer to each row (1, 2, 3, 4, ...). No two rows will have the same number.
    *   `RANK()`: Assigns a rank to each row. If there are ties, they receive the same rank, and the next rank is skipped. (1, 2, 2, 4, ...)
    *   `DENSE_RANK()`: Similar to `RANK()`, but the next rank is not skipped after a tie. (1, 2, 2, 3, ...)

2.  **Positional Functions:**
    *   `LAG(column, offset, default_value)`: Gets the value of `column` from the row that is `offset` rows *before* the current row.
    *   `LEAD(column, offset, default_value)`: Gets the value of `column` from the row that is `offset` rows *after* the current row.

---

**SQL Examples**

Let's use our `employees` table.

| id  | name     | department | salary |
| --- | -------- | ---------- | ------ |
| 1   | Ann      | Sales      | 70000  |
| 2   | Ben      | Sales      | 80000  |
| 3   | Cindy    | Sales      | 70000  |
| 4   | David    | Engineering| 90000  |
| 5   | Grace    | Engineering| 120000 |
| 6   | Hugh     | Engineering| 90000  |

**Example 1: Ranking salaries within each department**
Find the top 2 highest-paid employees in each department.
```sql
WITH RankedEmployees AS (
    SELECT
        name,
        department,
        salary,
        RANK() OVER (PARTITION BY department ORDER BY salary DESC) as salary_rank
    FROM employees
)
SELECT name, department, salary, salary_rank
FROM RankedEmployees
WHERE salary_rank <= 2;
```
*   **Analysis:**
    1.  `PARTITION BY department`: Divides employees into two groups: 'Sales' and 'Engineering'.
    2.  `ORDER BY salary DESC`: Sorts employees within each group by salary in descending order.
    3.  `RANK()`: Calculates the rank.
    4.  The outer query (using a CTE, covered next) filters for those with a rank of 1 or 2.
*   **Result:**

    | name     | department | salary | salary_rank |
        | -------- | ---------- | ------ | ----------- |
    | Grace    | Engineering| 120000 | 1           |
    | David    | Engineering| 90000  | 2           |
    | Hugh     | Engineering| 90000  | 2           |
    | Ben      | Sales      | 80000  | 1           |
    | Ann      | Sales      | 70000  | 2           |
    | Cindy    | Sales      | 70000  | 2           |

**Comparing `ROW_NUMBER`, `RANK`, `DENSE_RANK`**
```sql
SELECT
    name,
    salary,
    ROW_NUMBER() OVER (ORDER BY salary DESC) AS row_num,
    RANK() OVER (ORDER BY salary DESC) AS rnk,
    DENSE_RANK() OVER (ORDER BY salary DESC) AS dense_rnk
FROM employees;
```
*   **Result:**

    | name     | salary | row_num | rnk | dense_rnk |
        | -------- | ------ | ------- | --- | --------- |
    | Grace    | 120000 | 1       | 1   | 1         |
    | David    | 90000  | 2       | 2   | 2         |
    | Hugh     | 90000  | 3       | 2   | 2         |
    | Ben      | 80000  | 4       | 4   | 3         |
    | Ann      | 70000  | 5       | 5   | 4         |
    | Cindy    | 70000  | 6       | 5   | 4         |

**Example 2: Comparing salary to the previous employee**
```sql
SELECT
    name,
    department,
    salary,
    LAG(salary, 1, 0) OVER (PARTITION BY department ORDER BY salary) AS previous_salary
FROM employees;
```
*   **Analysis:** Compare each person's salary with the next lowest paid person *within the same department*.
*   **Result:**

    | name     | department | salary | previous_salary |
        | -------- | ---------- | ------ | --------------- |
    | David    | Engineering| 90000  | 0               |
    | Hugh     | Engineering| 90000  | 90000           |
    | Grace    | Engineering| 120000 | 90000           |
    | Ann      | Sales      | 70000  | 0               |
    | Cindy    | Sales      | 70000  | 70000           |
    | Ben      | Sales      | 80000  | 70000           |

---

**Mini-Exercise**

Given a `monthly_sales` table (month, revenue), write a query to display each month's revenue and a new column `growth_percentage` that shows the percentage growth compared to the previous month.

**Solution**
```sql
WITH PreviousMonthSales AS (
    SELECT
        month,
        revenue,
        LAG(revenue, 1, 0) OVER (ORDER BY month) AS previous_revenue
    FROM monthly_sales
)
SELECT
    month,
    revenue,
    ( (revenue - previous_revenue) / previous_revenue ) * 100 AS growth_percentage
FROM PreviousMonthSales
WHERE previous_revenue > 0;
```
*   **Analysis:** Use `LAG` to get the previous month's revenue. Then use an outer query (or a CTE) to perform the percentage calculation.

---

**Interview Questions**

*   **Question:** "What is the difference between `RANK()` and `DENSE_RANK()`? Give an example."
    *   **Answer:** Both are used for ranking and assign the same rank to tied values. The difference is in the next rank assigned after a tie. `RANK` skips subsequent ranks (1, 2, 2, 4), while `DENSE_RANK` does not (1, 2, 2, 3). For example, if ranking racers and two people tie for second place, `RANK` would place the next person in fourth (as there is no third place), while `DENSE_RANK` would place them in third.

*   **Question:** "What is the purpose of `PARTITION BY` in a window function? How is it different from `GROUP BY`?"
    *   **Answer:** `PARTITION BY` divides the data set into smaller groups, and the window function is applied independently to each group. It's similar to `GROUP BY` in that it groups data, but the fundamental difference is that `PARTITION BY` **does not reduce the number of rows returned**. It retains all original rows and simply adds a new column with the result of the window function for each row.

*   **Question:** "How would you solve the 'Top-N-per-Group' problem in SQL?"
    *   **Answer:** This is the classic problem for which Window Functions are the perfect solution. I would use a ranking function like `RANK()` or `ROW_NUMBER()` with `PARTITION BY` on the group column and `ORDER BY` on the ranking column. Then, I would use an outer query or a CTE to filter for rows where the rank is less than or equal to N.

---

**Best Practices & Common Mistakes**

*   **Best Practice:** When solving problems related to ranking, row-to-row comparisons, or running totals, think of Window Functions first. They often provide a much cleaner and more efficient solution than self-joins or correlated subqueries.
*   **Best Practice:** `PARTITION BY` can operate on multiple columns, e.g., `PARTITION BY department, job_title`.
*   **Common Mistake:** Forgetting the `ORDER BY` clause in ranking functions. `ORDER BY` is mandatory for `RANK`, `ROW_NUMBER`, etc., as they need to know the criteria on which to rank the rows.
*   **Common Mistake:** Trying to apply a `WHERE` clause directly to the result of a window function in the same `SELECT` statement.
    *   **WRONG:** `SELECT ..., RANK() OVER (...) AS rnk FROM ... WHERE rnk <= 5;`
    *   **Reason:** The `WHERE` clause is processed *before* the window functions are calculated.
    *   **RIGHT:** You must use a subquery or a CTE as shown in the examples.

### **3. CTE (Common Table Expression): `WITH` clause**

**Theory**

A **CTE (Common Table Expression)** is a named, **temporary result set** that you can reference within a subsequent `SELECT`, `INSERT`, `UPDATE`, or `DELETE` statement. It is defined using the `WITH` clause.

*   **Analogy:** Imagine you are solving a complex math problem. Instead of writing all the nested calculations into one single, giant formula, you would break it down:
    1.  Step 1: Calculate value A. (This is the first CTE)
    2.  Step 2: Use value A to calculate value B. (This is the second CTE, using the first)
    3.  Step 3: Use value B to get the final result. (This is the main `SELECT` statement)
*   CTEs help you do the same thing in SQL: break a complex query into logical, readable, and manageable steps.

**Why Use CTEs?**

1.  **Improved Readability:** This is the biggest benefit. CTEs allow you to name your subqueries, helping others (and your future self) understand the logic of a complex query.
2.  **Reusability:** You can define a CTE once and reference it multiple times in the main query.
3.  **Recursive Queries:** This is the most unique and powerful capability of CTEs, allowing you to process tree-like or hierarchical data structures (e.g., an organizational chart, nested product categories).

**Basic Syntax:**
```sql
WITH cte_name_1 AS (
    -- SELECT statement to define the first CTE
),
cte_name_2 AS (
    -- Another SELECT, which can reference cte_name_1
)
-- The main SELECT/UPDATE/DELETE statement, which can reference both CTEs
SELECT * FROM cte_name_2;
```

---

**Example 1: Non-recursive CTE**

Let's revisit the "find the top 2 highest-paid employees in each department" problem. We used a CTE to solve it before. Let's analyze it more closely.

```sql
-- Step 1: Create a CTE to rank employees
WITH RankedEmployees AS (
    SELECT
        name,
        department,
        salary,
        ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC) as rn
    FROM employees
)
-- Step 2: Use that CTE to filter for the final result
SELECT name, department, salary
FROM RankedEmployees
WHERE rn <= 2;
```
*   **Without a CTE:** You would have to write a nested subquery, which is much harder to read:
    ```sql
    SELECT name, department, salary
    FROM (
        SELECT
            name,
            department,
            salary,
            ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC) as rn
        FROM employees
    ) AS Subquery
    WHERE rn <= 2;
    ```
*   Clearly, the CTE version is cleaner and much easier to understand.

---

**Example 2: Recursive CTE**

This is an advanced but extremely useful feature. A recursive CTE is one that references itself. It must have a structure of two parts joined by `UNION ALL`.

1.  **Anchor Member:** A non-recursive `SELECT` statement that defines the initial result set (the starting point of the recursion).
2.  **Recursive Member:** A `SELECT` statement that references the CTE itself and `JOIN`s with another table to produce the next result set.

**Classic Problem:** Displaying an employee hierarchy tree. Let's say we have a `staff` table with columns: `staff_id`, `staff_name`, `manager_id` (which refers to the `staff_id` of the manager).

| staff_id | staff_name | manager_id |
| -------- | ---------- | ---------- |
| 1        | CEO        | NULL       |
| 2        | Director A | 1          |
| 3        | Director B | 1          |
| 4        | Manager A1 | 2          |
| 5        | Staff A1.1 | 4          |

```sql
WITH RECURSIVE EmployeeHierarchy AS (
    -- 1. Anchor Member: Find the root (the CEO, who has no manager)
    SELECT
        staff_id,
        staff_name,
        manager_id,
        0 AS level -- Add a column to track the level
    FROM staff
    WHERE manager_id IS NULL

    UNION ALL

    -- 2. Recursive Member: Find the direct reports
    SELECT
        s.staff_id,
        s.staff_name,
        s.manager_id,
        eh.level + 1 -- Increment the level by 1
    FROM staff AS s
    -- JOIN back to the CTE to find employees whose manager_id
    -- is the staff_id from the previous iteration's result
    INNER JOIN EmployeeHierarchy AS eh ON s.manager_id = eh.staff_id
)
-- Final query to display the result
SELECT * FROM EmployeeHierarchy;
```
*   **How it works:**
    1.  **Iteration 0:** The anchor member runs, finding the CEO (`staff_id = 1`, `level = 0`). The temporary result is `{(1, 'CEO', NULL, 0)}`.
    2.  **Iteration 1:** The recursive member runs, `JOIN`ing the `staff` table with the temporary result above (`eh.staff_id = 1`). It finds people whose `manager_id = 1` (Director A and B). The temporary result is appended: `{(2, 'Dir A', 1, 1), (3, 'Dir B', 1, 1)}`.
    3.  **Iteration 2:** The recursive member runs again, `JOIN`ing `staff` with the latest results (`eh.staff_id IN (2, 3)`). It finds Manager A1. The result is appended: `{(4, 'Mgr A1', 2, 2)}`.
    4.  This process continues until the recursive member finds no new rows.
*   **Result:**

    | staff_id | staff_name   | manager_id | level |
    | -------- | ------------ | ---------- | ----- |
    | 1        | CEO          | NULL       | 0     |
    | 2        | Director A   | 1          | 1     |
    | 3        | Director B   | 1          | 1     |
    | 4        | Manager A1   | 2          | 2     |
    | 5        | Staff A1.1   | 4          | 3     |

---

**Mini-Exercise**

Using CTEs, write a query to find all departments whose total salary is higher than the average total salary of all departments.

**Solution**

```sql
-- Step 1: CTE to calculate total salary for each department
WITH DepartmentSalaries AS (
    SELECT
        department_id,
        SUM(salary) AS total_salary
    FROM employees
    GROUP BY department_id
),
-- Step 2: CTE to calculate the average of those total salaries
AverageSalary AS (
    SELECT AVG(total_salary) AS avg_total_salary
    FROM DepartmentSalaries
)
-- Step 3: Main query to compare and get the result
SELECT ds.department_id, ds.total_salary
FROM DepartmentSalaries AS ds
JOIN AverageSalary AS avgs ON ds.total_salary > avgs.avg_total_salary;
```
*   **Analysis:** We've broken the problem into clear, logical steps. The `DepartmentSalaries` CTE pre-calculates the totals, and the `AverageSalary` CTE uses that result to find the average. The final query performs the comparison.

---

**Interview Questions**

*   **Question:** "What is the main benefit of using a CTE over a nested subquery (derived table)?"
    *   **Answer:** The main benefit is **readability and maintainability**. CTEs allow us to name logical blocks, like naming functions in programming, which makes complex queries much easier to understand. Additionally, a CTE can be referenced multiple times within the same query, whereas a derived table cannot. And most importantly, only CTEs support recursive queries.

*   **Question:** "Do CTEs improve performance?"
    *   **Answer:** Not necessarily. In most cases, a non-recursive CTE is just "syntactic sugar" — another way of writing a subquery. The query optimizer will often generate the exact same execution plan for both. Therefore, CTEs primarily improve code readability, not execution performance. However, in some complex cases, breaking down the logic might help the optimizer find a better plan, but this isn't their main purpose.

*   **Question:** "Explain how a recursive CTE works."
    *   **Answer:** A recursive CTE works by starting with a base result set (the Anchor Member), and then repeatedly combining (usually with a `JOIN`) the current result set with the source data to produce the next iteration's result set (the Recursive Member). This process stops when an iteration produces no new rows. It's very useful for traversing hierarchical data structures like org charts or category trees.

---

**Best Practices & Common Mistakes**

*   **Best Practice:** Use a CTE whenever you have a complex subquery or need to reference the same intermediate result set multiple times.
*   **Best Practice:** When writing a recursive CTE, always ensure there is a termination condition in the Recursive Member (usually the `JOIN` finding no new rows) to avoid an infinite loop. Some database systems offer a `MAXRECURSION` option as a safeguard.
*   **Common Mistake:** Thinking that a CTE is a "materialized" temporary table. Most databases execute a CTE like a macro, essentially "pasting" the CTE's code wherever it's called. It is not calculated once and stored (except in some special cases).
*   **Common Mistake:** Placing other statements between CTE definitions or between the CTE and the main statement. The entire `WITH ... SELECT` block must be a single, contiguous SQL statement.

### **4. Views**

**Theory**

A **View** is a **stored `SELECT` statement** in the database that is given a name and behaves like a **virtual table**.

*   **Analogy:** Imagine you're a business analyst who frequently runs a very complex 5-table `JOIN` to generate a regional sales report. Instead of retyping or copy-pasting that long query every day, you can save it under a simple name like `RegionalSalesReport`. From then on, you just need to run `SELECT * FROM RegionalSalesReport;`.
*   The View itself does not store any data. Every time you query a View, the database re-executes the original `SELECT` statement that defines it to produce the results.

**Benefits of Using Views:**

1.  **Simplicity:** Hides the complexity of `JOIN`s and calculation logic. End-users only need to interact with a simple "table."
2.  **Security:** You can create a View that exposes only certain columns or rows from the underlying tables. Instead of giving users direct access to sensitive tables (like an `employees` table with a `salary` column), you can grant them `SELECT` permission on a View that only shows `employee_name` and `department`.
3.  **Consistency:** Ensures that everyone uses the same complex business logic. If the logic for calculating revenue changes, you only need to update the View's definition in one place, and all reports using that View will be automatically updated.
4.  **Logical-Physical Independence:** If you refactor the base tables (e.g., split one table into two), you can maintain the old View. Client applications can continue to query the View without ever knowing that the underlying physical structure has changed.

**Syntax:**
```sql
-- Create a View
CREATE VIEW ViewName AS
SELECT column1, column2
FROM table_name
WHERE condition;

-- Use the View
SELECT * FROM ViewName;

-- Drop a View
DROP VIEW ViewName;
```

---

**Example**

Let's say we have `employees`, `departments`, and `salaries` tables. We want to create a View to show summary employee information without exposing the specific salary amount, showing a salary level instead (high, medium, low).

```sql
CREATE VIEW EmployeeSummary AS
SELECT
    e.employee_id,
    e.full_name,
    d.department_name,
    CASE
        WHEN s.amount > 100000 THEN 'High'
        WHEN s.amount > 60000 THEN 'Medium'
        ELSE 'Low'
    END AS salary_level
FROM
    employees AS e
JOIN
    departments AS d ON e.department_id = d.department_id
JOIN
    salaries AS s ON e.employee_id = s.employee_id
WHERE
    e.is_active = TRUE;
```
*   **Usage:** Now, instead of rewriting the entire query above, the HR department or analysts can simply run:
    ```sql
    SELECT * FROM EmployeeSummary WHERE department_name = 'Engineering';
    ```
*   They cannot see the original `salary` column. They also don't need to worry about the complex `JOIN` logic.

---

**Mini-Exercise**

Based on `posts` and `comments` tables (one post has many comments), create a View named `PostCommentCounts` to display `post_id`, `post_title`, and a new column `number_of_comments`.

**Solution**

```sql
CREATE VIEW PostCommentCounts AS
SELECT
    p.post_id,
    p.title AS post_title,
    COUNT(c.comment_id) AS number_of_comments
FROM
    posts AS p
LEFT JOIN -- Use LEFT JOIN to include posts with zero comments
    comments AS c ON p.post_id = c.post_id
GROUP BY
    p.post_id, p.title;
```
*   **Usage:** `SELECT * FROM PostCommentCounts ORDER BY number_of_comments DESC;`

---

**Interview Questions**

*   **Question:** "What is the difference between a View and a CTE?"
    *   **Answer:**
        *   **Lifecycle:** A View is a permanent database object; it is stored in the schema until it is explicitly `DROP`ped. A CTE exists only for the duration of the single SQL statement in which it is defined.
        *   **Purpose:** Views are created for long-term reusability and access control. CTEs are used to break down and clarify the logic of a single complex statement at the time of writing.
        *   **Storage:** Neither stores data (with the special exception of Materialized Views).

*   **Question:** "Can Views slow down performance? Why?"
    *   **Answer:** Yes, Views can be slow. Since a View is just a stored `SELECT` statement, if that underlying statement is inefficient (e.g., joins many large tables without proper indexes), then querying the View will also be inefficient. In particular, nesting Views (a View that queries another View) can make it difficult for the database's optimizer to create a good execution plan.

*   **Question:** "What is a Materialized View, and how is it different from a regular View?"
    *   **Answer:** This is an advanced question. Unlike a regular View (which is a virtual table), a **Materialized View** is a view whose results are **physically stored** on disk, just like a real table. It does not re-run the original `SELECT` query each time it's accessed. Instead, it returns the pre-calculated data. This makes reading from a Materialized View extremely fast. The trade-off is that the data can become stale and requires a mechanism to be "refreshed" periodically. They are commonly used in data warehousing systems.

---

**Best Practices & Common Mistakes**

*   **Best Practice:** Use Views to provide a stable API for the application layer. The application interacts with the Views, and you are free to change the underlying table structure without breaking the application.
*   **Best Practice:** Keep the logic in Views relatively simple. Avoid nesting too many Views. If a View becomes overly complex, consider if it's a sign of a poor underlying database design.
*   **Common Mistake:** Assuming Views are always fast. The performance of a View is entirely dependent on the performance of the `SELECT` statement that defines it. Always run `EXPLAIN` on queries against Views to check their performance.
*   **Common Mistake:** Trying to `INSERT`, `UPDATE`, or `DELETE` on complex Views. While some simple Views (typically based on a single table) can be updatable, Views with `JOIN`s, `GROUP BY`, aggregate functions, etc., are generally not. Attempting to do so can lead to unexpected behavior and errors.

---

### **5. Stored Procedures, Functions, Triggers (And When NOT to Use Them)**

**Theory**

These are blocks of code (usually written in the database's procedural language like PL/pgSQL or T-SQL) that are stored and executed directly on the database server. They allow you to encapsulate complex business logic within the database layer.

*   **Stored Procedure:**
    *   **What it is:** A named and saved collection of SQL statements and control logic. It can accept input and output parameters and perform complex operations (even transactions involving multiple `INSERT`s, `UPDATE`s, and `DELETE`s).
    *   **Analogy:** An API "endpoint" directly within the database. The application just needs to call the procedure's name and pass parameters, instead of sending a series of SQL statements.

*   **User-Defined Function (UDF):**
    *   **What it is:** Similar to a Stored Procedure but with one key difference: it **must always return a single value** (or a table).
    *   **Analogy:** A pure function in programming. It's often used inside `SELECT` statements to perform custom calculations.

*   **Trigger:**
    *   **What it is:** A special kind of Stored Procedure that is automatically executed when a DML event (`INSERT`, `UPDATE`, `DELETE`) occurs on a specific table.
    *   **Analogy:** An "event listener." For example: "Every time a new row is inserted into the `orders` table, automatically update the inventory count in the `products` table."

**Advantages:**
*   **Reduced Network Traffic:** Instead of sending many SQL statements back and forth, the application only needs to send a single `CALL` statement.
*   **Reusability and Encapsulation:** Business logic is defined once and can be called by many different applications.
*   **Security:** You can grant `EXECUTE` permissions on a Stored Procedure without granting permissions on the underlying tables it manipulates.
*   **Performance (sometimes):** The execution plan can be cached by the database, making subsequent calls faster.

**Disadvantages and WHEN NOT TO USE THEM:**
This is the most important part in modern practice.

1.  **Difficult to Maintain and Debug:** Writing and debugging PL/SQL code is often much harder than in modern programming languages (Python, Java, Go...). The tooling is also less mature.
2.  **Difficult Version Control:** Getting database code into systems like Git and managing changes and migrations is much more complex than with application code.
3.  **Poor Scalability:** Business logic in the database consumes the database server's resources (CPU, memory). The database is often the hardest component to scale in a system. Pushing heavy computational logic to application servers—which are easily scaled horizontally—is generally a better architecture.
4.  **Vendor Lock-in:** T-SQL code from SQL Server will not run on PostgreSQL (PL/pgSQL), and vice versa. If you want to change your database vendor, you have to rewrite all this logic.
5.  **"Magic" or Hidden Logic:** Especially with `Trigger`s. A new developer looking at the application code will have no idea why inserting a record also causes another table to change automatically. Triggers make the data flow opaque and hard to follow.

**The Practical Conclusion:**
In modern application development, the trend is to keep the database **"dumb"**. That is, the database should focus only on storing and retrieving data efficiently. Most (or all) business logic should be handled in the **application layer**.

**You should only use them when:**
*   The logic truly, absolutely must be executed at the database level to ensure data integrity that cannot be trusted to clients. (Example: a Trigger to update an `updated_at` column).
*   For tasks specific to DBAs (Database Administrators) or internal ETL (Extract, Transform, Load) jobs.
*   When network latency is an extremely critical issue and a Stored Procedure can significantly reduce round-trips.

---

**Interview Questions**

*   **Question:** "What's the main difference between a Stored Procedure and a Function?"
    *   **Answer:** The main difference is that a Function must return a value and is typically used inside a `SELECT` statement as part of an expression. A Stored Procedure is not required to return a value; it can perform a series of actions and is called as a standalone statement (`CALL` or `EXECUTE`).

*   **Question:** "Why are Triggers often considered dangerous or an 'anti-pattern'?"
    *   **Answer:** Triggers are considered dangerous because they create hidden "side effects." The logic executes automatically and is not explicit in the application code. This makes the system very hard to reason about and debug. A small change can cause a chain of cascading triggers, leading to unpredictable performance problems and even deadlocks.

*   **Question:** "In a microservices architecture, why is placing business logic in Stored Procedures a bad idea?"
    *   **Answer:** A microservices architecture promotes the idea that each service owns and manages its own data. If multiple services all call into Stored Procedures in a shared database, it creates a point of **tight coupling** at the data layer, which completely violates the principles of microservices. It breaks the independence and separate deployability of the services.

### **6. Transactions, ACID, and Rollback**

**Theory**

A **Transaction** is a **logical unit of work** that consists of one or more SQL statements. The key principle of a transaction is that it is **"all-or-nothing"**.

*   **Real-world Analogy:** A bank transfer from account A to account B. This work consists of two inseparable steps:
    1.  Debit money from account A.
    2.  Credit money to account B.
*   Imagine the system crashes after debiting A's money. Without a transaction, A's money would be lost, and B would never receive it. A transaction wraps both actions together, ensuring that if step 2 fails, step 1 will also be **undone (rolled back)**, returning account A to its original state.

**Transaction Control Language (TCL) Commands:**

*   **`BEGIN TRANSACTION` (or `START TRANSACTION`):** Marks the beginning of a transaction.
*   **`COMMIT`:** Permanently saves all changes made within the transaction to the database. The transaction ends successfully.
*   **`ROLLBACK`:** Undoes all changes that have been made within the transaction since the `BEGIN` statement. The database is returned to the state it was in before the transaction started. The transaction ends in failure.
*   **`SAVEPOINT name`:** Creates a "save point" within a transaction.
*   **`ROLLBACK TO SAVEPOINT name`:** Undoes changes back to a previously created `SAVEPOINT`, but does not undo the entire transaction.

**ACID - The "Oath" of Relational Databases**

ACID is an acronym for a set of four properties that guarantee transactions are processed reliably.

1.  **Atomicity:**
    *   Ensures the "all-or-nothing" principle. A transaction is an indivisible unit. It is either 100% `COMMIT`ted successfully or completely `ROLLBACK`ed.
    *   **Example:** The bank transfer.

2.  **Consistency:**
    *   Ensures that a transaction can only bring the database from one valid state to another. All database constraints must be upheld.
    *   **Example:** If there is a `balance >= 0` constraint on a bank account, a transaction that attempts to withdraw money and cause a negative balance will fail and be `ROLLBACK`ed, ensuring the database remains consistent.

3.  **Isolation:**
    *   Ensures that concurrently executing transactions do not interfere with each other. From one transaction's perspective, it feels as though it is the only transaction running on the system.
    *   **Example:** While a transfer from A to B is in progress (but not yet `COMMIT`ted), another transaction trying to read the balances of A and B will either see the balances *before* the transaction started, or it will have to wait until *after* the transaction completes. It will not see an inconsistent intermediate state. (The degree of this isolation is configurable, see next section).

4.  **Durability:**
    *   Ensures that once a transaction has been `COMMIT`ted, its changes are permanent and will not be lost, even in the event of a system failure (power outage, server crash). This is typically achieved by writing to transaction logs on disk before the actual data is updated.

---

**SQL Example**

Let's simulate the money transfer transaction. Assume an `accounts` table with `account_id` and `balance` columns.

```sql
START TRANSACTION;

-- In a real app, you would SELECT the current balances into variables
-- SELECT balance FROM accounts WHERE account_id = 'A'; -- Assume 1000
-- SELECT balance FROM accounts WHERE account_id = 'B'; -- Assume 500

-- Assume transfer amount is 200

-- Step 1: Debit from account A
UPDATE accounts SET balance = balance - 200 WHERE account_id = 'A';

-- Step 2: Credit to account B
UPDATE accounts SET balance = balance + 200 WHERE account_id = 'B';

-- Check conditions (e.g., A's balance must not be negative)
-- IF (SELECT balance FROM accounts WHERE account_id = 'A') < 0 THEN
--     -- If an error occurs, undo everything
--     ROLLBACK;
-- ELSE
--     -- If everything is okay, confirm the transaction
--     COMMIT;
-- END IF;
```
*   **How it works:** If any error occurs between `START TRANSACTION` and `COMMIT` (network error, logic error, server crash), the entire transaction will be automatically `ROLLBACK`ed when the database restarts, ensuring no money is "in limbo." If the application code detects a logical error (like insufficient funds), it can explicitly call `ROLLBACK`.

---

**Mini-Exercise**

You are writing a script to enroll a new student in a course. This involves two steps:
1.  Insert a new record into the `registrations` table (student_id, course_id).
2.  Update the `courses` table by decreasing the `slots_available` by 1.

Write the SQL (in pseudo-code form) using a transaction to ensure both steps happen safely. You need to handle the case where there are no available slots (`slots_available <= 0`).

**Solution**

```sql
START TRANSACTION;

-- Get the number of available slots
-- available_slots = SELECT slots_available FROM courses WHERE course_id = ?;

IF available_slots > 0 THEN
    -- Step 1: Decrement the number of available slots
    UPDATE courses SET slots_available = slots_available - 1 WHERE course_id = ?;

    -- Step 2: Add the registration record
    INSERT INTO registrations (student_id, course_id) VALUES (?, ?);

    -- If both commands succeed, confirm
    COMMIT;
ELSE
    -- If no slots are available, undo
    -- (in reality, no changes were made, but rollback is good practice)
    ROLLBACK;
    -- Report error to user: "Course is full"
END IF;
```

---

**Interview Questions**

*   **Question:** "Explain the four ACID properties using a real-world example."
    *   **Answer:** Use the example of booking a movie ticket. You select two seats and click "pay."
        *   **Atomicity:** Either you get the tickets AND those two seats are marked as "sold," or your payment fails AND those two seats remain "available." There is no in-between state.
        *   **Consistency:** The system does not allow you to book a seat that is already sold. Your transaction must adhere to this rule.
        *   **Isolation:** If you and another person click "pay" for the exact same seat at the exact same time, isolation ensures that only one of your transactions will succeed. The other will fail.
        *   **Durability:** Once you receive the ticket confirmation email, your booking is saved securely. Even if the cinema's server loses power right after, your ticket is still valid.

*   **Question:** "What do `COMMIT` and `ROLLBACK` do?"
    *   **Answer:** `COMMIT` is the command to permanently save the changes of a transaction. `ROLLBACK` is the command to undo all changes made within that transaction, returning the database to the state it was in before the transaction began.

*   **Question:** "What happens to an open transaction if the connection to the database is suddenly lost?"
    *   **Answer:** Most database systems will automatically perform a `ROLLBACK` for any open, uncommitted transaction when the session that opened it is disconnected. This is a safety mechanism to ensure there are no "orphaned" or "in-limbo" transactions.

---

**Best Practices & Common Mistakes**

*   **Best Practice:** Keep transactions as short and fast as possible. A long-running transaction holds locks on resources (rows, tables) for longer, which increases the likelihood of contention and reduces the overall performance of the system.
*   **Best Practice:** Never leave a transaction open while waiting for user interaction (e.g., waiting for user input). Gather all necessary information first, then start and finish the transaction quickly.
*   **Common Mistake:** **Autocommit.** Most SQL clients default to `autocommit = ON`, meaning every single SQL statement you type is automatically wrapped in a transaction and immediately `COMMIT`ted. This is convenient for simple queries but very dangerous when you need to perform multiple dependent operations. Always remember to turn `autocommit` off or use `START TRANSACTION` explicitly when needed.
*   **Common Mistake:** Performing DDL operations (like `CREATE TABLE`, `ALTER TABLE`) inside a transaction. While some databases support this, many others (like MySQL) will perform an implicit `COMMIT` before running the DDL command, which can break your transaction's logic.

---

### **7. Isolation Levels & Concurrency Issues**

**Theory**

**Isolation** in ACID is not an absolute "on or off" concept. The SQL standard defines four **Isolation Levels**, which allow you to trade off between **consistency** and **performance**. The higher the isolation level, the safer and more consistent the data, but the lower the performance due to increased locking by the database.

**Concurrency Phenomena:**
When multiple transactions run at the same time, the following problems can occur if the isolation level is not high enough:

1.  **Dirty Read:**
    *   Transaction A modifies a row but has **not yet `COMMIT`ted**. Transaction B reads that row and sees A's **uncommitted** value. If A then `ROLLBACK`s, B has read "garbage" data that never officially existed.
    *   **Example:** A updates a product price to $120 (uncommitted). B reads the price of $120 and processes an order. A encounters an error and rolls back the price to $100. B has now sold the product at the wrong price.

2.  **Non-Repeatable Read:**
    *   Transaction A reads a row. Transaction B then **updates or deletes** that row and `COMMIT`s. If Transaction A reads the same row again, it will see a different value or that the row has disappeared.
    *   **Example:** A reads an account balance of $1000. B makes a deposit and commits, raising the balance to $1500. A reads the balance again and sees $1500. The data has changed within A's single transaction.

3.  **Phantom Read:**
    *   Transaction A runs a query with a `WHERE` clause (e.g., `SELECT COUNT(*) FROM employees WHERE department = 'Sales'`), and gets a result of 20. Transaction B then **inserts a new employee** into the 'Sales' department and `COMMIT`s. If Transaction A runs the exact same `SELECT` query again, it will see a result of 21. A "phantom" row has appeared.

**The Four Isolation Levels:**

| Isolation Level          | Dirty Read | Non-Repeatable Read | Phantom Read | Performance |
| ------------------------ | ---------- | ------------------- | ------------ | ----------- |
| **READ UNCOMMITTED**     | Allowed    | Allowed             | Allowed      | Highest     |
| **READ COMMITTED**       | Prevented  | Allowed             | Allowed      | High        |
| **REPEATABLE READ**      | Prevented  | Prevented           | Allowed      | Medium      |
| **SERIALIZABLE**         | Prevented  | Prevented           | Prevented    | Lowest      |

*   **READ UNCOMMITTED:** Almost never used in practice due to the high risk.
*   **READ COMMITTED:** The **default level** for most databases (PostgreSQL, SQL Server, Oracle). It's a good balance: you never read uncommitted data, but data can change during your transaction.
*   **REPEATABLE READ:** The **default level** for MySQL (InnoDB). It guarantees that if you re-read the same row, you will always see the same data. However, new rows can still be inserted by other transactions.
*   **SERIALIZABLE:** The highest level. It guarantees that the result of concurrent transactions is equivalent to them running one after another, sequentially. It eliminates all concurrency phenomena but comes at the cost of the lowest performance and a higher chance of `serialization failure` errors.

---

**Interview Questions**

*   **Question:** "Explain the three concurrency phenomena: Dirty Read, Non-Repeatable Read, and Phantom Read."
    *   **Answer:** (Use the definitions and examples provided above).
        *   **Dirty Read:** Reading uncommitted data.
        *   **Non-Repeatable Read:** Data you already read is `UPDATE`d or `DELETE`d by another transaction.
        *   **Phantom Read:** New rows are `INSERT`ed that match a query you run again.

*   **Question:** "What is the default isolation level for PostgreSQL/MySQL, and what problems does it solve?"
    *   **Answer:**
        *   **PostgreSQL:** The default is `READ COMMITTED`. It prevents Dirty Reads but still allows Non-Repeatable Reads and Phantom Reads.
        *   **MySQL (InnoDB):** The default is `REPEATABLE READ`. It prevents Dirty Reads and Non-Repeatable Reads, but Phantom Reads can still occur (although InnoDB's `next-key locking` mechanism helps mitigate this issue).

*   **Question:** "When would you need to raise the isolation level to `SERIALIZABLE`?"
    *   **Answer:** You should only use `SERIALIZABLE` when there is an extremely strict business requirement that a series of operations must occur on a completely unchanging "snapshot" of the data. For example, a complex reporting system that must guarantee all metrics are calculated based on the exact same dataset at a single point in time, with no changes (including `INSERT`s) interfering between calculation steps. However, the application must be designed to be able to retry transactions that fail due to `serialization failure`.