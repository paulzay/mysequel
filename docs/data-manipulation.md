# DML – Data Manipulation Language

DML commands read and modify the **data** inside tables.  
Unlike DDL, DML statements can be wrapped in transactions and rolled back.

---

## SELECT

Retrieves rows from one or more tables.

```sql
-- All columns, all rows
SELECT * FROM employees;

-- Specific columns
SELECT first_name, last_name, salary FROM employees;

-- Column alias
SELECT first_name AS "First Name", salary * 12 AS annual_salary
FROM employees;

-- Eliminate duplicate rows
SELECT DISTINCT dept_id FROM employees;

-- Limit the number of rows returned
SELECT * FROM employees LIMIT 10;
SELECT * FROM employees LIMIT 10 OFFSET 20;   -- rows 21-30

-- Simple condition
SELECT * FROM employees WHERE salary > 50000;

-- Sort results
SELECT * FROM employees ORDER BY last_name ASC, first_name DESC;

-- Aggregate: count rows per department
SELECT dept_id, COUNT(*) AS headcount
FROM employees
GROUP BY dept_id
HAVING COUNT(*) > 5;
```

See [clauses-and-functions.md](clauses-and-functions.md) for `WHERE`, `JOIN`, `GROUP BY`, and built-in functions.

---

## INSERT

Adds one or more new rows to a table.

### Insert a single row

```sql
INSERT INTO employees (first_name, last_name, email, salary, hire_date, dept_id)
VALUES ('Alice', 'Smith', 'alice@example.com', 72000.00, '2024-01-15', 3);
```

### Insert multiple rows at once

```sql
INSERT INTO employees (first_name, last_name, dept_id)
VALUES
    ('Bob',   'Jones',  2),
    ('Carol',  'White',  1),
    ('David', 'Brown',  3);
```

### Insert from a SELECT (copy rows)

```sql
INSERT INTO employees_backup (first_name, last_name, email)
    SELECT first_name, last_name, email
    FROM   employees
    WHERE  dept_id = 4;
```

### INSERT … ON DUPLICATE KEY UPDATE

Updates the row if the primary/unique key already exists, otherwise inserts.

```sql
INSERT INTO employees (id, first_name, last_name, salary)
VALUES (101, 'Alice', 'Smith', 75000.00)
ON DUPLICATE KEY UPDATE salary = 75000.00;
```

### REPLACE INTO

Deletes the existing row (if any) and inserts the new one.

```sql
REPLACE INTO employees (id, first_name, last_name, salary)
VALUES (101, 'Alice', 'Smith', 75000.00);
```

---

## UPDATE

Modifies existing rows in a table.

```sql
-- Update a single column for specific rows
UPDATE employees
SET salary = 80000.00
WHERE id = 101;

-- Update multiple columns at once
UPDATE employees
SET salary    = 80000.00,
    hire_date = '2024-06-01'
WHERE id = 101;

-- Update all rows (no WHERE — use with caution!)
UPDATE employees SET salary = salary * 1.05;

-- Update with a JOIN
UPDATE employees e
JOIN   departments d ON e.dept_id = d.id
SET    e.salary = e.salary * 1.10
WHERE  d.name = 'Sales';

-- Limit the number of rows affected
UPDATE employees SET salary = salary * 1.05 LIMIT 100;
```

> **Tip:** Always test your `WHERE` clause with a `SELECT` first to confirm which rows will change.

---

## DELETE

Removes rows from a table.

```sql
-- Delete specific rows
DELETE FROM employees WHERE id = 101;

-- Delete using a JOIN
DELETE e
FROM   employees e
JOIN   departments d ON e.dept_id = d.id
WHERE  d.name = 'Temp';

-- Delete all rows (slower than TRUNCATE; can be rolled back)
DELETE FROM employees;

-- Limit rows deleted
DELETE FROM employees WHERE hire_date < '2010-01-01' LIMIT 500;
```

> **Warning:** A `DELETE` without a `WHERE` clause removes every row. Always double-check before running.
