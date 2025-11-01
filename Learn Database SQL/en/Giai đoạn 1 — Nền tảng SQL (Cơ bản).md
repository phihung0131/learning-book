### **1. Database, DBMS, and the Relational Model**

**Theory**

*   **Database:** Imagine a database as a massive, scientifically organized digital filing cabinet for storing information. Instead of paper, it contains data. For example, an e-commerce website's database holds information about users, products, orders, etc. Its main purpose is to store, retrieve, and manage data efficiently.

*   **DBMS (Database Management System):** If the database is the filing cabinet, the DBMS is the librarian who manages it. A DBMS is software that allows us to interact with the database: add new data, read it, update it, or delete it. Popular DBMSs include MySQL, PostgreSQL, SQL Server, and Oracle. We give commands to the librarian (DBMS) using a language called SQL.

*   **Relational Model:** This is the most common way to organize data. In this model, data is arranged into tables, similar to spreadsheets in Excel.

    *   **Table (also called Relation or Entity):** Represents a specific type of object. For example, a `STUDENTS` table, a `PRODUCTS` table. Each table stores data about a single subject.
    *   **Column (also called Field or Attribute):** Represents a property of the object. In the `STUDENTS` table, columns might be `student_id`, `full_name`, and `date_of_birth`. Each column has a specific data type (e.g., `full_name` is text, `date_of_birth` is a date type).
    *   **Row (also called Record or Tuple):** Represents a specific instance of the object. Each row in the `STUDENTS` table contains the information for one unique student.
    *   **Primary Key (PK):** A column (or a set of columns) that uniquely identifies each row in a table. For example, `student_id` would be the primary key for the `STUDENTS` table because no two students can have the same ID.
    *   **Foreign Key (FK):** A column in one table that is the Primary Key in another table. It's used to create a link between the two tables. For example, an `ORDERS` table might have a `customer_id` column, which links to the `id` column in the `CUSTOMERS` table.

**Visual Example**

Consider the `Users` table:

| id (PK) | user_name | email                  | creation_date |
| --- | --------- | ---------------------- | ------------- |
| 1   | an_nguyen | an.nguyen@example.com  | 2024-10-25    |
| 2   | binh_le   | binh.le@example.com    | 2024-10-26    |
| 3   | chi_tran  | chi.tran@example.com   | 2024-10-27    |

*   **Table:** `Users`
*   **Columns:** `id`, `user_name`, `email`, `creation_date`
*   **Row:** The row with `id` = 1 is a record, containing all the information about the user "an_nguyen".

---

**Mini-Exercise**

You are designing a database for a simple blog. Identify:
1.  What are the main **tables** you might have?
2.  For each table, list a few possible **columns**.

**Solution**

1.  The main tables could be: `authors` (to store author information) and `posts` (to store the articles).
2.  `authors` table: `id` (PK), `author_name`, `email`.
    `posts` table: `id` (PK), `title`, `content`, `publication_date`, `author_id` (FK to link to the authors table).

---

**Interview Questions**

*   **Question:** "What is the difference between a Database and a DBMS?"
    *   **Answer:** A database is the container for the data, like a warehouse. A DBMS is the software system that manages that warehouse, allowing us to put goods in, take them out, and organize them. We don't interact directly with the database; we interact through the DBMS.

*   **Question:** "Explain the concepts: Table, Column, and Row."
    *   **Answer:** In the relational database model, data is organized into Tables. Each Table represents a type of entity (e.g., Products). Each Column represents an attribute of that entity (e.g., Product Name, Price). Each Row represents a specific instance of that entity (e.g., one specific iPhone 15 Pro Max).

---

**Best Practices & Common Mistakes**

*   **Best Practice:** Always name tables and columns consistently. Common conventions are:
    *   Table names are plural nouns (e.g., `users`, `products`).
    *   Column names use `snake_case` (e.g., `user_name`, `creation_date`).
    *   This makes SQL code easier to read and maintain.

*   **Common Mistake:** Using spaces or special characters in table/column names. This forces you to always enclose the names in double quotes or backticks, which is inconvenient and error-prone. For example, instead of `User Name`, use `user_name`.

---

### **2. Types of SQL Commands: DDL, DML, DQL, DCL, TCL**

**Theory**

SQL has five main groups of commands, each with a specific purpose:

1.  **DQL (Data Query Language):** Used to "ask" for and retrieve data from the database. This is the group you will use the most.
    *   Main command: `SELECT`.
    *   Analogy: "Hey librarian, show me a list of all the books."

2.  **DML (Data Manipulation Language):** Used to change the data *inside* the tables.
    *   Commands: `INSERT` (add new), `UPDATE` (modify), `DELETE` (remove).
    *   Analogy: Adding a new book to the shelf, correcting a book's title, or throwing a book away.

3.  **DDL (Data Definition Language):** Used to define and manage the *structure* of the database.
    *   Commands: `CREATE` (create tables, databases), `ALTER` (modify table structure), `DROP` (delete tables, databases), `TRUNCATE` (delete all data in a table).
    *   Analogy: Building new bookshelves, changing the size of a shelf, or completely demolishing a row of shelves.

4.  **DCL (Data Control Language):** Used to manage user access rights.
    *   Commands: `GRANT` (give permissions), `REVOKE` (take away permissions).
    *   Analogy: Giving someone a library card and specifying which sections they are allowed to read books in.

5.  **TCL (Transaction Control Language):** Used to manage "transactions" (a group of operations).
    *   Commands: `COMMIT` (permanently save changes), `ROLLBACK` (undo changes), `SAVEPOINT` (create a temporary save point).
    *   Analogy: When you're shopping, you gather multiple items. `COMMIT` is like successfully paying for all items. `ROLLBACK` is like canceling the entire shopping cart if your credit card fails.

---

**Mini-Exercise**

Classify the following SQL statements into DDL, DML, DQL, or DCL:
1.  `UPDATE products SET price = 500;`
2.  `ALTER TABLE users ADD COLUMN age INT;`
3.  `GRANT SELECT ON customers TO new_employee;`
4.  `SELECT first_name, last_name FROM employees;`

**Solution**

1.  `UPDATE`: DML (Data Manipulation)
2.  `ALTER TABLE`: DDL (Data Definition)
3.  `GRANT`: DCL (Data Control)
4.  `SELECT`: DQL (Data Query)

---

**Interview Questions**

*   **Question:** "What is the difference between `DELETE`, `TRUNCATE`, and `DROP`?"
    *   **Answer:** This is a classic question.
        *   `DELETE FROM table_name;`: This is a DML command. It removes rows from a table, one by one. It can remove some rows (with a `WHERE` clause) or all rows. It logs each deletion, which makes it slower, and it can be rolled back.
        *   `TRUNCATE TABLE table_name;`: This is a DDL command. It quickly removes *all* data from a table by deallocating the data pages. It doesn't log individual row deletions, so it's very fast and generally cannot be rolled back. It also resets any auto-incrementing identity values.
        *   `DROP TABLE table_name;`: This is a DDL command. It deletes the entire table, including its structure and data. The table will no longer exist.

---

**Best Practices & Common Mistakes**

*   **Best Practice:** Be extremely cautious when using DDL commands (`ALTER`, `DROP`) on a production database, as they can cause data loss or lock tables for extended periods. Always have a backup before making major structural changes.
*   **Common Mistake:** Using `DELETE` without a `WHERE` clause to clear a table instead of `TRUNCATE`. `TRUNCATE` is far more efficient for this purpose.

---

### **3. CRUD Operations: `SELECT`, `INSERT`, `UPDATE`, `DELETE`**

CRUD is an acronym for the four basic operations in data management: **C**reate, **R**ead, **U**pdate, **D**elete. In SQL, they correspond to the following commands:

*   **C**reate -> `INSERT`
*   **R**ead -> `SELECT`
*   **U**pdate -> `UPDATE`
*   **D**elete -> `DELETE`

We will practice on a `products` table with the following structure:
`id` (integer, auto-incrementing), `name` (text), `price` (decimal), `quantity` (integer).

```sql
-- DDL: Command to create the practice table
CREATE TABLE products (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    price DECIMAL(10, 2) NOT NULL,
    quantity INT DEFAULT 0
);
```

**Examples**

1.  **`INSERT` (Create new data)**

    ```sql
    -- Insert a new product
    INSERT INTO products (name, price, quantity)
    VALUES ('Laptop Dell XPS 15', 1599.99, 50);

    -- Insert multiple products at once
    INSERT INTO products (name, price, quantity)
    VALUES
        ('Keychron K2 Mechanical Keyboard', 89.99, 120),
        ('Logitech MX Master 3S Mouse', 99.99, 200);
    ```
    *   **Explanation:** We specify the columns to insert data into, then provide the corresponding values in the `VALUES` clause. The order of the values must match the order of the listed columns.

2.  **`SELECT` (Read data)**

    ```sql
    -- Get all information for all products
    SELECT * FROM products;

    -- Get only the name and price of the products
    SELECT name, price FROM products;
    ```
    *   **Explanation:** `SELECT *` is a way to get all columns. For better performance, you should only select the columns you actually need.

3.  **`UPDATE` (Update data)**

    ```sql
    -- Update the price for the product with id = 1
    UPDATE products
    SET price = 1649.99
    WHERE id = 1;

    -- Decrease the price of all keyboards by 10%
    UPDATE products
    SET price = price * 0.9
    WHERE name LIKE 'Keychron%';
    ```
    *   **Explanation:** The `SET` clause specifies which column to update and its new value. **Crucially important:** The `WHERE` clause filters which rows should be updated. **If you omit `WHERE`, all rows in the table will be updated!**

4.  **`DELETE` (Delete data)**

    ```sql
    -- Delete the product with id = 3
    DELETE FROM products
    WHERE id = 3;
    ```
    *   **Explanation:** Similar to `UPDATE`, the `WHERE` clause is critical for specifying which row to delete. **Omitting `WHERE` will delete all data from the table!**

---

**Mini-Exercise**

Given the `products` table above, write SQL queries to:
1.  Add a new product: 'LG UltraGear Monitor', price 350.00, quantity 75.
2.  Increase the quantity of the 'Laptop Dell XPS 15' to 55.
3.  Read the name and quantity of all products that cost less than 100.00.

**Solution**

1.  `INSERT INTO products (name, price, quantity) VALUES ('LG UltraGear Monitor', 350.00, 75);`
2.  `UPDATE products SET quantity = 55 WHERE name = 'Laptop Dell XPS 15';`
3.  `SELECT name, quantity FROM products WHERE price < 100.00;`

---

**Interview Questions**

*   **Question:** "What are the consequences of running an `UPDATE` or `DELETE` statement without a `WHERE` clause?"
    *   **Answer:** Without a `WHERE` clause, the command is applied to all rows in the table. An `UPDATE` will change the entire column to the same value, and a `DELETE` will wipe out all data in the table. This is one of the most dangerous mistakes one can make in SQL.

*   **Question:** "In an `INSERT` statement, what happens if I don't specify the column list?"
    *   **Answer:** If you omit the column list (e.g., `INSERT INTO products VALUES (...)`), you must provide a value for *every* column in the table, in the exact order they are defined in the table's structure. This practice is discouraged because it is very brittle. If someone later adds a new column to the table, that `INSERT` statement will immediately fail.

---

**Best Practices & Common Mistakes**

*   **Best Practice:** Before running a critical `UPDATE` or `DELETE`, first write a `SELECT` statement with the same `WHERE` condition. For example, before running `DELETE FROM products WHERE price > 2000;`, first run `SELECT * FROM products WHERE price > 2000;` to see exactly which rows will be affected.
*   **Common Mistakes:**
    *   Forgetting `WHERE` in `UPDATE`/`DELETE`.
    *   Incorrect order of values in `INSERT` when not listing column names.
    *   Using `SELECT *` in application code. This increases the load on the database and network and can cause errors if the table structure changes. Always specify the columns you need.

### **4. Filtering (WHERE), Sorting (ORDER BY), and Pagination (LIMIT / OFFSET)**

**Theory**

Once you know how to retrieve data with `SELECT`, the next step is to control *what* data is retrieved, in *what order*, and in *what quantity*.

*   **`WHERE` (Filtering):** The `WHERE` clause acts as a filter. You set conditions, and only rows that satisfy all those conditions are returned.
    *   **Analogy:** When you go to a library, instead of asking for "all books," you say "get me books by author X, published after 2020." `author = X` and `publication_year > 2020` are the conditions in your `WHERE` clause.
    *   Common operators: `=`, `!=` (or `<>`), `>`, `<`, `>=`, `<=`, `AND`, `OR`, `NOT`.

*   **`ORDER BY` (Sorting):** Used to sort the returned results by one or more columns.
    *   **Analogy:** After the librarian finds your books, you ask them to "arrange these books on the table alphabetically by title."
    *   `ASC` (Ascending): Sorts in increasing order (the default, can be omitted).
    *   `DESC` (Descending): Sorts in decreasing order.

*   **`LIMIT` / `OFFSET` (Pagination):** Used to restrict the number of results and retrieve "pages" of data. This is an extremely important technique in web applications to avoid loading thousands of records at once. In some SQL dialects like SQL Server, `TOP` is used instead of `LIMIT`.
    *   `LIMIT N`: Get only the first `N` rows.
    *   `OFFSET M`: Skip the first `M` rows before starting to retrieve.
    *   **Analogy:** You tell the librarian, "Of the sorted books, just show me the first 5" (`LIMIT 5`). The next time you come back, you say, "Skip the first 5 I already saw, and give me the next 5" (`LIMIT 5 OFFSET 5`).

**SQL Example**

Let's continue with the `products` table. Assume it has the following data:

| id  | name                       | price      | quantity |
| --- | -------------------------- | ---------- | -------- |
| 1   | Laptop Dell XPS 15         | 1649.99    | 55       |
| 2   | Keychron K2 Mechanical Keyboard | 89.99      | 120      |
| 4   | LG UltraGear Monitor       | 350.00     | 75       |
| 5   | Logitech Wireless Mouse    | 35.99      | 300      |
| 6   | Samsung 1TB SSD            | 120.00     | 150      |
| 7   | Macbook Pro M3 Laptop      | 2499.00    | 30       |

1.  **Filtering with `WHERE`**

    ```sql
    -- Get products that cost more than 1000.00
    SELECT name, price FROM products WHERE price > 1000.00;
    ```
    **Result:**
    ```
    name                  | price
    ----------------------+-----------
    Laptop Dell XPS 15    | 1649.99
    Macbook Pro M3 Laptop | 2499.00
    ```

    ```sql
    -- Get products that are 'Laptops' AND have a quantity less than 50
    SELECT name, quantity FROM products WHERE name LIKE 'Laptop%' AND quantity < 50;
    ```
    **Result:**
    ```
    name                  | quantity
    ----------------------+----------
    Macbook Pro M3 Laptop | 30
    ```

2.  **Sorting with `ORDER BY`**

    ```sql
    -- Get all products, sorted by price descending
    SELECT name, price FROM products ORDER BY price DESC;
    ```
    **Result:**
    ```
    name                       | price
    ---------------------------+-----------
    Macbook Pro M3 Laptop      | 2499.00
    Laptop Dell XPS 15         | 1649.99
    LG UltraGear Monitor       | 350.00
    Samsung 1TB SSD            | 120.00
    Keychron K2 Mechanical Keyboard | 89.99
    Logitech Wireless Mouse    | 35.99
    ```

3.  **Paginating with `LIMIT` and `OFFSET`**

    ```sql
    -- Get the 3 most expensive products
    SELECT name, price
    FROM products
    ORDER BY price DESC
    LIMIT 3;
    ```
    **Result:**
    ```
    name                       | price
    ---------------------------+-----------
    Macbook Pro M3 Laptop      | 2499.00
    Laptop Dell XPS 15         | 1649.99
    LG UltraGear Monitor       | 350.00
    ```

    ```sql
    -- Get the 2nd page of the product list, with 2 products per page, sorted alphabetically
    -- Page 1: LIMIT 2 OFFSET 0
    -- Page 2: LIMIT 2 OFFSET 2
    SELECT id, name
    FROM products
    ORDER BY name ASC
    LIMIT 2 OFFSET 2;
    ```
    **Result:** (After sorting by name, the first two products are Keychron and LG)
    ```
    id  | name
    ----+-----------------------
    1   | Laptop Dell XPS 15
    7   | Macbook Pro M3 Laptop
    ```

---

**Mini-Exercise**

Using the `products` table above, write a single SQL query to:
*   Find the 2 products with the highest stock quantity.
*   Only consider products that cost less than 150.00.
*   Display their name, price, and quantity.

**Solution**

```sql
SELECT name, price, quantity
FROM products
WHERE price < 150.00
ORDER BY quantity DESC
LIMIT 2;
```
*   **Analysis:**
    1.  `FROM products`: Identifies the working table.
    2.  `WHERE price < 150.00`: Filters for products that meet the price condition.
    3.  `ORDER BY quantity DESC`: Sorts the filtered products by quantity in descending order.
    4.  `LIMIT 2`: Takes the top 2 rows from the sorted result.
    5.  `SELECT name, price, quantity`: Selects the columns to display.

---

**Interview Questions**

*   **Question:** "What is the logical order of execution for a `SELECT` query containing `WHERE`, `ORDER BY`, and `LIMIT`?"
    *   **Answer:** Although we write `SELECT` first, the database doesn't execute it in that order. The logical order is:
        1.  `FROM`: Identifies the source tables.
        2.  `JOIN`: Combines tables.
        3.  `WHERE`: Filters rows.
        4.  `GROUP BY`: Aggregates rows into groups.
        5.  `HAVING`: Filters groups.
        6.  `SELECT`: Selects the columns (or computes new values).
        7.  `ORDER BY`: Sorts the final result set.
        8.  `LIMIT` / `OFFSET`: Takes a slice of the sorted result.
    *   Understanding this is crucial for query optimization. For instance, you know that `WHERE` is applied before `ORDER BY`, so an efficient filter reduces the number of rows that need to be sorted, speeding up the query.

*   **Question:** "Why can pagination with `OFFSET` become very slow for pages far into the result set (e.g., page 1000)?"
    *   **Answer:** When you use `LIMIT 10 OFFSET 10000`, the database still has to fetch and sort all 10,010 rows, then discard the first 10,000, and return only the last 10. For a large `OFFSET`, this "discarding" process is very resource-intensive. A more efficient alternative is "keyset pagination" (or "cursor-based pagination"), which uses the value of the last item on the previous page to fetch the next one. For example: `WHERE id > [last_id_from_previous_page] ORDER BY id ASC LIMIT 10`.

---

**Best Practices & Common Mistakes**

*   **Best Practice:** Always use `ORDER BY` when using `LIMIT` / `OFFSET`. Without `ORDER BY`, the row order is not guaranteed. The database might return different results for the same query on different runs, making your pagination meaningless and inconsistent.
*   **Best Practice:** Use parentheses `()` when combining `AND` and `OR` in a `WHERE` clause to ensure the logic executes as intended and avoid ambiguity. For example: `WHERE (category = 'Laptop' AND price > 1000) OR (quantity = 0)`.
*   **Common Mistake:** Miscalculating the `OFFSET` value. The general formula for page `p` (1-indexed) with `n` items per page is: `OFFSET (p - 1) * n`. For example, to get page 3 with 20 items/page, the `OFFSET` is `(3 - 1) * 20 = 40`.
*   **Common Mistake:** Applying functions or operations to a column in the `WHERE` clause (e.g., `WHERE YEAR(order_date) = 2024`). This often prevents the database from using an index on that column, slowing down the query. A better approach is: `WHERE order_date >= '2024-01-01' AND order_date < '2025-01-01'`.

### **5. Common Functions and Operators: `COUNT`, `SUM`, `AVG`, `MIN`, `MAX`, `LIKE`, `BETWEEN`, `IN`, `DISTINCT`**

**Theory**

This section introduces tools that help you process and summarize data, as well as more powerful ways to filter it. We can divide them into two groups:

**1. Aggregate Functions:**
These are functions that operate on a set of rows and return a single summary value.
*   `COUNT()`: Counts the number of rows.
*   `SUM()`: Calculates the sum of values in a column (numeric columns only).
*   `AVG()`: Calculates the average value (numeric columns only).
*   `MIN()`: Finds the minimum value.
*   `MAX()`: Finds the maximum value.

**Analogy:** Imagine you're a teacher looking at the class gradebook. Aggregate functions are like calculating:
*   How many students are in the class? (`COUNT`)
*   What is the total score of the entire class? (`SUM`)
*   What is the class's average score? (`AVG`)
*   Who has the lowest score? (`MIN`)
*   Who has the highest score? (`MAX`)

**2. Advanced Filtering Operators and Keywords:**
These are tools used in the `WHERE` clause to create more complex filtering conditions.
*   `LIKE`: Matches a string against a pattern. Very useful for text searches.
*   `BETWEEN`: Checks if a value falls within a given range (inclusive of both endpoints).
*   `IN`: Checks if a value matches any value in a list.
*   `DISTINCT`: Removes duplicate values from the result set.

---

**SQL Examples**

Let's create an `orders` table to practice:
```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_name VARCHAR(100),
    product_category VARCHAR(50),
    total_amount DECIMAL(12, 2),
    order_date DATE
);

INSERT INTO orders VALUES
(1, 'John Smith', 'Electronics', 150.00, '2024-10-01'),
(2, 'Jane Doe', 'Apparel', 85.00, '2024-10-03'),
(3, 'Peter Jones', 'Electronics', 320.00, '2024-10-05'),
(4, 'Mary Brown', 'Home Goods', 55.00, '2024-10-05'),
(5, 'John Smith', 'Home Goods', 120.00, '2024-10-08'),
(6, 'Chris Green', 'Electronics', 780.00, '2024-10-12');
```

**Examples of Aggregate Functions:**

```sql
-- How many total orders are there?
SELECT COUNT(*) FROM orders;
-- Result: 6

-- What is the total revenue from all orders?
SELECT SUM(total_amount) FROM orders;
-- Result: 1490.00

-- What is the average revenue per order?
SELECT AVG(total_amount) FROM orders;
-- Result: 248.33

-- What are the highest and lowest order values?
SELECT MAX(total_amount), MIN(total_amount) FROM orders;
-- Result: 780.00, 55.00
```

**Examples of Filtering Operators and `DISTINCT`:**

```sql
-- Find all customers whose last name is 'Smith'
SELECT DISTINCT customer_name FROM orders WHERE customer_name LIKE '% Smith';```
*   **Explanation:** `LIKE '% Smith'` finds all strings that end with ' Smith'. The `%` is a wildcard that represents zero or more characters. `DISTINCT` ensures that 'John Smith' appears only once, even though he has two orders.
*   **Result:**
    ```
    customer_name
    -------------
    John Smith
    ```

```sql
-- Find orders placed between Oct 1, 2024, and Oct 5, 2024
SELECT order_id, total_amount, order_date
FROM orders
WHERE order_date BETWEEN '2024-10-01' AND '2024-10-05';
```
*   **Explanation:** `BETWEEN` is inclusive of the start and end dates.
*   **Result:** This will return orders with `order_id` 1, 2, 3, and 4.

```sql
-- Find orders in the 'Electronics' or 'Home Goods' categories
SELECT order_id, product_category
FROM orders
WHERE product_category IN ('Electronics', 'Home Goods');
```
*   **Explanation:** `IN` is a more concise and often more efficient way of writing `WHERE product_category = 'Electronics' OR product_category = 'Home Goods'`.
*   **Result:** This will return orders with `order_id` 1, 3, 4, 5, and 6.

---

**Mini-Exercise**

Using the `orders` table above, write an SQL query to:
*   Calculate the total revenue (`total_amount`) just from 'Electronics' orders placed in October 2024.

**Solution**

```sql
SELECT SUM(total_amount)
FROM orders
WHERE
    product_category = 'Electronics'
    AND order_date BETWEEN '2024-10-01' AND '2024-10-31';
```
*   **Analysis:**
    1.  `FROM orders`: Work on the `orders` table.
    2.  `WHERE product_category = 'Electronics'`: Filter for 'Electronics' orders only.
    3.  `AND order_date BETWEEN ...`: Further filter for orders within October.
    4.  `SELECT SUM(total_amount)`: Calculate the sum of `total_amount` for the filtered rows.
*   **Result:** `150.00 + 320.00 + 780.00 = 1250.00`

---

**Interview Questions**

*   **Question:** "What is the difference between `COUNT(*)` and `COUNT(column_name)`?"
    *   **Answer:** `COUNT(*)` counts all rows in the result set, regardless of column values. `COUNT(column_name)` only counts rows where `column_name` has a non-`NULL` value. If a column can contain `NULL`s, these two counts might produce different results.

*   **Question:** "When would you use `IN` instead of multiple `OR` conditions?"
    *   **Answer:** I would almost always prefer `IN` when checking a column against a list of static values. `IN` makes the SQL statement much more concise and readable. Performance-wise, most modern database systems optimize `IN` very well, often making it equivalent to or better than multiple `OR` conditions.

*   **Question:** "How would you find all products with 'SSD' anywhere in their name?"
    *   **Answer:** I would use the `LIKE` operator with wildcards on both sides: `WHERE product_name LIKE '%SSD%'`. The leading `%` allows any characters before 'SSD', and the trailing `%` allows any characters after it.

---

**Best Practices & Common Mistakes**

*   **Best Practice:** Use `IN` instead of multiple `OR`s for better readability.
*   **Best Practice:** `COUNT(1)` is often considered functionally equivalent to `COUNT(*)` in terms of performance. Both mean "count the number of rows." Using `COUNT(*)` is often semantically clearer.
*   **Common Mistake:** Using a leading wildcard (`%` or `_`) in a `LIKE` search (e.g., `'%search_term'`). This prevents the database from using standard indexes on that column, often forcing a full table scan, which severely degrades performance on large tables.
*   **Common Mistake:** Confusion about `BETWEEN`. Remember that `BETWEEN` is inclusive. For `DATETIME` data types, this can lead to unexpected results. For example, `BETWEEN '2024-10-01' AND '2024-10-05'` will include transactions at `00:00:00` on the 5th but miss any that happened later that day. A safer approach is: `WHERE date_column >= '2024-10-01' AND date_column < '2024-10-06'`.

### **6. `GROUP BY` + `HAVING` (Business Use Cases)**

**Theory**

*   **`GROUP BY`:** This clause allows you to collapse multiple rows that have the same value in one or more columns into a single summary row. `GROUP BY` is almost always used with aggregate functions (`COUNT`, `SUM`, `AVG`, etc.) to perform calculations on each group.

    *   **Business Analogy:** Imagine you're a manager of a retail chain. Your data table contains thousands of individual transactions from all branches. Your boss doesn't want to see every single transaction. Instead, they ask: "What is the total sales revenue for *each branch*?"
        *   The action of "grouping all transactions for each branch" is `GROUP BY branch_name`.
        *   The action of "calculating the total revenue" is `SUM(sales_revenue)`.

*   **`HAVING` (Filtering Groups):** If `WHERE` is a filter for individual rows *before* they are grouped, `HAVING` is a filter for the groups *after* they have been created by `GROUP BY`. You can only use `HAVING` with conditions based on the results of aggregate functions.

    *   **Business Analogy:** After you've generated the report of total sales for each branch, your boss asks, "Now, only show me the branches with total sales *over $1,000,000*."
        *   You can't use `WHERE` here, because "total sales" isn't a column in your original transaction table; it's a value calculated after grouping.
        *   You must use `HAVING SUM(sales_revenue) > 1000000` to filter the groups (branches) that meet this condition.

**Logical Order of Execution:** `FROM` -> `WHERE` -> `GROUP BY` -> `HAVING` -> `SELECT` -> `ORDER BY`.

---

**SQL Examples**

We'll continue using the `orders` table.

| order_id | customer_name | product_category | total_amount | order_date |
| -------- | ------------- | ---------------- | ------------ | ---------- |
| 1        | John Smith    | Electronics      | 150.00       | 2024-10-01 |
| 2        | Jane Doe      | Apparel          | 85.00        | 2024-10-03 |
| 3        | Peter Jones   | Electronics      | 320.00       | 2024-10-05 |
| 4        | Mary Brown    | Home Goods       | 55.00        | 2024-10-05 |
| 5        | John Smith    | Home Goods       | 120.00       | 2024-10-08 |
| 6        | Chris Green   | Electronics      | 780.00       | 2024-10-12 |

1.  **Basic `GROUP BY` Example:** Count the number of orders for each product category.

    ```sql
    SELECT product_category, COUNT(order_id) AS number_of_orders
    FROM orders
    GROUP BY product_category;
    ```
    *   **Explanation:** The database scans the `orders` table, groups rows with the same `product_category`. Then, for each group, it applies the `COUNT` function to count the number of orders.
    *   **Result:**
        ```
        product_category | number_of_orders
        -----------------+--------------------
        Electronics      | 3
        Apparel          | 1
        Home Goods       | 2
        ```

2.  **`GROUP BY` with Multiple Aggregates:** Calculate total and average revenue for each category.

    ```sql
    SELECT
        product_category,
        SUM(total_amount) AS total_revenue,
        AVG(total_amount) AS average_revenue
    FROM orders
    GROUP BY product_category;
    ```
    *   **Result:**
        ```
        product_category | total_revenue | average_revenue
        -----------------+---------------+-----------------
        Electronics      | 1250.00       | 416.67
        Apparel          | 85.00         | 85.00
        Home Goods       | 175.00        | 87.50
        ```

3.  **`GROUP BY` and `HAVING` Example:** Find categories with a total revenue over $200.

    ```sql
    SELECT product_category, SUM(total_amount) AS total_revenue
    FROM orders
    GROUP BY product_category
    HAVING SUM(total_amount) > 200;
    ```
    *   **Explanation:** This query works just like example 2, but after the groups are created and calculated, the `HAVING` clause filters them, keeping only those with a `total_revenue` greater than 200.
    *   **Result:**
        ```
        product_category | total_revenue
        -----------------+---------------
        Electronics      | 1250.00
        ```

4.  **Combining `WHERE` and `HAVING`:** Calculate total spending for each customer, but only count orders over $100, and finally, only show customers whose total spending (after filtering) is greater than $500.

    ```sql
    SELECT customer_name, SUM(total_amount) AS total_spending
    FROM orders
    WHERE total_amount > 100 -- Filter individual rows BEFORE grouping
    GROUP BY customer_name
    HAVING SUM(total_amount) > 500; -- Filter groups AFTER grouping
    ```
    *   **Result:**
        ```
        customer_name | total_spending
        --------------+----------------
        Chris Green   | 780.00
        ```
    *   **Logical Analysis:**
        1.  `WHERE total_amount > 100` removes the orders from 'Jane Doe' ($85) and 'Mary Brown' ($55).
        2.  The remaining rows are grouped by `customer_name`.
        3.  `SUM` is calculated for these groups: Smith (150 + 120 = 270), Jones (320), Green (780).
        4.  `HAVING SUM(...) > 500` filters these groups, keeping only 'Chris Green'.

---

**Mini-Exercise**

Using the `orders` table, write an SQL query to:
1.  Find customers who have purchased more than once.
2.  Display the customer's name and their number of purchases.

**Solution**

```sql
SELECT customer_name, COUNT(order_id) AS purchase_count
FROM orders
GROUP BY customer_name
HAVING COUNT(order_id) > 1;
```
*   **Analysis:**
    1.  `GROUP BY customer_name`: Groups all orders by each customer.
    2.  `COUNT(order_id)`: Counts the number of orders in each group.
    3.  `HAVING COUNT(order_id) > 1`: Filters for groups (customers) with a count greater than 1.
*   **Result:**
    ```
    customer_name | purchase_count
    --------------+----------------
    John Smith    | 2
    ```

---

**Interview Questions**

*   **Question:** "What is the core difference between `WHERE` and `HAVING`?"
    *   **Answer:**
        1.  **Timing of Filtering:** `WHERE` filters data at the row-level *before* any grouping occurs. `HAVING` filters data at the group-level *after* rows have been aggregated by `GROUP BY`.
        2.  **Filtering Target:** `WHERE` operates on regular columns. `HAVING` operates on aggregate functions (`SUM`, `COUNT`, etc.). You cannot write `WHERE COUNT(*) > 10`; you must write `HAVING COUNT(*) > 10`.

*   **Question:** "Can I use a column in the `SELECT` clause that is not in the `GROUP BY` clause and is not an aggregate function? Why?"
    *   **Answer:** According to the SQL standard, no. The reason is ambiguity. When you collapse many rows into one, the database doesn't know which value to display for that column. For example, if you `GROUP BY product_category` and `SELECT customer_name`, the 'Electronics' group has three different customers ('Smith', 'Jones', 'Green'), and the database doesn't know which name to pick. (Note: Some database systems like older versions of MySQL have a "relaxed" mode that allows this, but it often returns an indeterminate value from the group and is considered bad practice).

---

**Best Practices & Common Mistakes**

*   **Best Practice:** Always prefer filtering with `WHERE` if possible. `WHERE` eliminates unnecessary rows early, reducing the amount of data that `GROUP BY` has to process, which improves query performance. Only use `HAVING` when you need to filter on the result of an aggregate function.
*   **Common Mistake:** Using `WHERE` with an aggregate function. Example: `WHERE SUM(total_amount) > 100000`. This is a syntax error because `WHERE` operates on individual rows and doesn't know what `SUM` is.
*   **Common Mistake:** Writing a `SELECT` with an aggregate function but forgetting `GROUP BY`. Example: `SELECT product_category, SUM(total_amount) FROM orders;`. Most databases will throw an error because they don't know which groups you want to sum over.
*   **Common Mistake:** Having columns in `SELECT` that are inconsistent with `GROUP BY`. The golden rule is: **Any column in the `SELECT` clause must either be in the `GROUP BY` clause or be wrapped in an aggregate function.**

### **7. Joins: `INNER`, `LEFT`, `RIGHT`, `FULL`, `CROSS` — When to Use Each Type**

**Theory**

In a well-designed database, data is often split into multiple tables to avoid redundancy (this is called "normalization"). For example, instead of storing the department name and address in the employee table, we create a separate `departments` table and only store `department_id` in the `employees` table.

`JOIN` is the command that allows us to "merge" these tables back together based on a common column (usually a primary key-foreign key relationship) to create a temporary result table containing information from all joined tables.

**Analogy:** Imagine you have two lists:
1.  A `STUDENTS` list (student_id, student_name, class_id).
2.  A `CLASSES` list (class_id, class_name, teacher_name).

`JOIN` helps you answer the question, "Who is the teacher of the student 'John Smith'?" by connecting these two lists via `class_id`.

**Types of JOINs and Venn Diagrams**

| JOIN Type        | Explanation                                                              | Venn Diagram (A and B are two tables)                                        |
| ---------------- | ------------------------------------------------------------------------ | ---------------------------------------------------------------------------- |
| **`INNER JOIN`** | Returns rows when there is a match in **both** tables.                   | The intersecting area in the middle.                                         |
| **`LEFT JOIN`**  | Returns **all** rows from the left table, and the matched rows from the right table. If there's no match, the right side columns will be `NULL`. | The entire left circle, plus the intersection.                               |
| **`RIGHT JOIN`** | Returns **all** rows from the right table, and the matched rows from the left table. If there's no match, the left side columns will be `NULL`. | The entire right circle, plus the intersection.                              |
| **`FULL JOIN`**  | Returns **all** rows when there is a match in either of the tables. If no match, `NULL` is used for the missing side. | The entirety of both circles A and B.                                        |
| **`CROSS JOIN`** | Returns every possible combination of rows from the two tables (Cartesian product). | Not representable by a Venn diagram. If table A has M rows and B has N rows, the result has M * N rows. |

---

**SQL Examples**

Let's create two tables, `employees` and `departments`, to practice.

```sql
CREATE TABLE departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(50)
);

CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    employee_name VARCHAR(50),
    department_id INT -- Foreign key, can be NULL
);

INSERT INTO departments VALUES
(1, 'Sales'),
(2, 'Engineering'),
(3, 'Human Resources'),
(4, 'Marketing'); -- This department has no employees yet

INSERT INTO employees VALUES
(101, 'Ann Smith', 1),
(102, 'Ben Lee', 2),
(103, 'Cindy Tran', 2),
(104, 'David Pham', 1),
(105, 'Holly Mai', NULL); -- This employee has not been assigned a department
```

**1. `INNER JOIN` (Only employees with a department)**
*   **Goal:** Get a list of employees and their corresponding department names. Employees without a department or departments without employees will be omitted.

```sql
SELECT e.employee_name, d.department_name
FROM employees AS e
INNER JOIN departments AS d ON e.department_id = d.department_id;
```
*   **Explanation:** `AS e` and `AS d` are aliases to make the query shorter. The `ON` clause specifies the join condition: only merge rows where the `department_id` is the same in both tables.
*   **Result:**
    ```
    employee_name | department_name
    --------------+-----------------
    Ann Smith     | Sales
    Ben Lee       | Engineering
    Cindy Tran    | Engineering
    David Pham    | Sales
    ```

**2. `LEFT JOIN` (All employees, whether they have a department or not)**
*   **Goal:** Get a list of *all* employees and their department names. If an employee has no department, the `department_name` will be `NULL`.

```sql
SELECT e.employee_name, d.department_name
FROM employees AS e
LEFT JOIN departments AS d ON e.department_id = d.department_id;
```
*   **Result:**
    ```
    employee_name | department_name
    --------------+-----------------
    Ann Smith     | Sales
    Ben Lee       | Engineering
    Cindy Tran    | Engineering
    David Pham    | Sales
    Holly Mai     | NULL
    ```

**3. `RIGHT JOIN` (All departments, whether they have employees or not)**
*   **Goal:** Get a list of *all* departments and the employees in them. If a department has no employees, the `employee_name` will be `NULL`.

```sql
SELECT e.employee_name, d.department_name
FROM employees AS e
RIGHT JOIN departments AS d ON e.department_id = d.department_id;
```
*   **Result:**
```
    employee_name | department_name
    --------------+-----------------
    Ann Smith     | Sales
    David Pham    | Sales
    Ben Lee       | Engineering
    Cindy Tran    | Engineering
    NULL          | Human Resources
    NULL          | Marketing
```

**4. `FULL OUTER JOIN` (All employees AND all departments)**
*   **Goal:** Get all data from both tables, matching where possible.

```sql
-- Note: MySQL does not support FULL OUTER JOIN. You can emulate it by UNIONing a LEFT JOIN and a RIGHT JOIN.
-- With PostgreSQL or SQL Server, you can run:
SELECT e.employee_name, d.department_name
FROM employees AS e
FULL OUTER JOIN departments AS d ON e.department_id = d.department_id;
```
*   **Result:**
    ```
    employee_name | department_name
    --------------+-----------------
    Ann Smith     | Sales
    Ben Lee       | Engineering
    Cindy Tran    | Engineering
    David Pham    | Sales
    Holly Mai     | NULL
    NULL          | Human Resources
    NULL          | Marketing
    ```

---

**Mini-Exercise**

Using the two tables above, write an SQL query to find the departments that have **no** employees.

**Solution**

This is a classic use case for a `LEFT JOIN` (or `RIGHT JOIN`) combined with a `WHERE` clause.

```sql
SELECT d.department_name
FROM departments AS d
LEFT JOIN employees AS e ON d.department_id = e.department_id
WHERE e.employee_id IS NULL;
```
*   **Analysis:**
    1.  `departments AS d LEFT JOIN employees AS e`: We take all departments (the left table) and join them with their corresponding employees.
    2.  For departments that have no employees ('Human Resources', 'Marketing'), all columns from the `employees` table (including `e.employee_id`) will be `NULL`.
    3.  `WHERE e.employee_id IS NULL`: We filter for exactly those `NULL` rows, which represent the departments without employees.
*   **Result:**
    ```
    department_name
    ---------------
    Human Resources
    Marketing
    ```

---

**Interview Questions**

*   **Question:** "What's the main difference between `INNER JOIN` and `LEFT JOIN`? Give a real-world example of when you must use a `LEFT JOIN`."
    *   **Answer:** `INNER JOIN` returns only the rows that have a match in both tables (the intersection). `LEFT JOIN` returns all rows from the left table, regardless of whether they have a match in the right table. A classic example is finding "customers who have never placed an order." You would `LEFT JOIN` from the `customers` table to the `orders` table. The customers for whom the `order_id` is `NULL` are the ones who have never bought anything.

*   **Question:** "What is the difference between putting a filter condition in the `ON` clause versus the `WHERE` clause of a `LEFT JOIN`?"
    *   **Answer:** This is an advanced question that tests deep understanding.
        *   Condition in `ON`: Is applied *during* the join process. For a `LEFT JOIN`, it will try to find a row in the right table that matches both the join key and this additional condition. If it can't, it still keeps the left row and fills the right-side columns with `NULL`.
        *   Condition in `WHERE`: Is applied *after* the join is complete. If you filter on a column from the right table in the `WHERE` clause, it will eliminate all rows that don't satisfy the condition, including the rows that the `LEFT JOIN` created with `NULL` values. This effectively turns the `LEFT JOIN` into an `INNER JOIN`.

*   **Question:** "Is `RIGHT JOIN` really necessary?"
    *   **Answer:** Logically, `RIGHT JOIN` is not strictly necessary because any `RIGHT JOIN` can be rewritten as a `LEFT JOIN` by simply swapping the order of the tables. However, `LEFT JOIN` is far more commonly used and is considered more readable by convention.

---

**Best Practices & Common Mistakes**

*   **Best Practice:** Always use aliases for your tables when joining more than one. It makes your queries shorter, more readable, and avoids ambiguity errors when tables have columns with the same name.
*   **Best Practice:** Be explicit by writing `INNER JOIN` instead of just `JOIN`. Although `JOIN` defaults to `INNER JOIN`, being explicit immediately clarifies your intent to other developers.
*   **Common Mistake:** Accidentally turning a `LEFT JOIN` into an `INNER JOIN` as explained in the interview question. For example, to find all employees and show their department if it's 'Engineering':
    *   **WRONG:** `... LEFT JOIN departments d ON ... WHERE d.department_name = 'Engineering'`. This will remove 'Holly Mai' because her `department_name` is `NULL`, which is not equal to 'Engineering'.
    *   **RIGHT:** `... LEFT JOIN departments d ON e.department_id = d.department_id AND d.department_name = 'Engineering'`. This keeps 'Holly Mai' and only shows the department name if it is 'Engineering'.
*   **Common Mistake:** Unintentionally performing a `CROSS JOIN`. If you forget the `ON` clause when joining two large tables, the database may perform a `CROSS JOIN`, creating a massive result set and potentially crashing the system.