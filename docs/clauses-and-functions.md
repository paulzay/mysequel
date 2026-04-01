# Clauses & Functions

This page covers the key **clauses** used inside `SELECT`, `UPDATE`, and `DELETE` statements,  
as well as MySQL's most-used **built-in functions**.

---

## WHERE

Filters rows based on a condition.

```sql
-- Comparison operators
SELECT * FROM employees WHERE salary > 50000;
SELECT * FROM employees WHERE dept_id = 3;
SELECT * FROM employees WHERE hire_date >= '2023-01-01';
SELECT * FROM employees WHERE email IS NULL;
SELECT * FROM employees WHERE email IS NOT NULL;

-- Logical operators
SELECT * FROM employees WHERE dept_id = 2 AND salary > 60000;
SELECT * FROM employees WHERE dept_id = 1 OR dept_id = 2;
SELECT * FROM employees WHERE NOT dept_id = 5;

-- BETWEEN (inclusive)
SELECT * FROM employees WHERE salary BETWEEN 40000 AND 70000;

-- IN / NOT IN
SELECT * FROM employees WHERE dept_id IN (1, 2, 3);
SELECT * FROM employees WHERE dept_id NOT IN (4, 5);

-- LIKE (pattern matching)
SELECT * FROM employees WHERE last_name LIKE 'S%';   -- starts with S
SELECT * FROM employees WHERE email LIKE '%@gmail.com';
SELECT * FROM employees WHERE first_name LIKE '_ob'; -- 3-letter name ending 'ob'

-- Subquery in WHERE
SELECT * FROM employees
WHERE dept_id = (SELECT id FROM departments WHERE name = 'Engineering');
```

---

## ORDER BY

Sorts the result set.

```sql
SELECT * FROM employees ORDER BY last_name ASC;
SELECT * FROM employees ORDER BY salary DESC;
SELECT * FROM employees ORDER BY dept_id ASC, salary DESC;

-- Sort by column position (column 3 = salary)
SELECT id, last_name, salary FROM employees ORDER BY 3 DESC;
```

---

## LIMIT / OFFSET

Controls how many rows are returned (useful for pagination).

```sql
SELECT * FROM employees LIMIT 10;           -- first 10 rows
SELECT * FROM employees LIMIT 10 OFFSET 20; -- rows 21-30 (page 3 of 10)

-- Shorthand: LIMIT offset, count
SELECT * FROM employees LIMIT 20, 10;
```

---

## GROUP BY

Groups rows that share a common value so aggregate functions can be applied per group.

```sql
-- Number of employees per department
SELECT dept_id, COUNT(*) AS headcount
FROM employees
GROUP BY dept_id;

-- Average salary per department
SELECT dept_id, AVG(salary) AS avg_salary
FROM employees
GROUP BY dept_id;

-- Group by multiple columns
SELECT dept_id, hire_date, COUNT(*) AS hired
FROM employees
GROUP BY dept_id, hire_date;
```

---

## HAVING

Filters **groups** (applied after `GROUP BY`). Use `WHERE` to filter rows, `HAVING` to filter groups.

```sql
-- Departments with more than 5 employees
SELECT dept_id, COUNT(*) AS headcount
FROM employees
GROUP BY dept_id
HAVING COUNT(*) > 5;

-- Departments whose average salary exceeds 70000
SELECT dept_id, AVG(salary) AS avg_salary
FROM employees
GROUP BY dept_id
HAVING AVG(salary) > 70000;
```

---

## JOIN

Combines rows from two or more tables based on a related column.

### INNER JOIN — only matching rows

```sql
SELECT e.first_name, e.last_name, d.name AS department
FROM   employees e
INNER JOIN departments d ON e.dept_id = d.id;
```

### LEFT JOIN — all rows from the left table, matching rows from the right

```sql
SELECT e.first_name, d.name AS department
FROM   employees e
LEFT JOIN departments d ON e.dept_id = d.id;
-- Employees without a department still appear (department = NULL)
```

### RIGHT JOIN — all rows from the right table, matching rows from the left

```sql
SELECT e.first_name, d.name AS department
FROM   employees e
RIGHT JOIN departments d ON e.dept_id = d.id;
-- Departments with no employees still appear
```

### CROSS JOIN — every combination of rows (cartesian product)

```sql
SELECT e.first_name, p.name AS project
FROM employees e
CROSS JOIN projects p;
```

### Self JOIN — join a table to itself

```sql
SELECT e.first_name AS employee, m.first_name AS manager
FROM   employees e
LEFT JOIN employees m ON e.manager_id = m.id;
```

---

## Subqueries

A query nested inside another query.

```sql
-- In WHERE
SELECT * FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);

-- In FROM (inline view / derived table)
SELECT dept_id, avg_sal
FROM (
    SELECT dept_id, AVG(salary) AS avg_sal
    FROM employees
    GROUP BY dept_id
) AS dept_averages
WHERE avg_sal > 60000;

-- Correlated subquery
SELECT e.first_name, e.salary
FROM employees e
WHERE e.salary = (
    SELECT MAX(salary)
    FROM employees
    WHERE dept_id = e.dept_id
);
```

---

## Aggregate Functions

Run calculations across a set of rows.

| Function | Description |
|----------|-------------|
| `COUNT(*)` | Count all rows |
| `COUNT(col)` | Count non-NULL values in a column |
| `SUM(col)` | Sum of values |
| `AVG(col)` | Average of values |
| `MIN(col)` | Minimum value |
| `MAX(col)` | Maximum value |

```sql
SELECT
    COUNT(*)           AS total_employees,
    COUNT(email)       AS with_email,
    SUM(salary)        AS payroll,
    AVG(salary)        AS avg_salary,
    MIN(salary)        AS lowest_salary,
    MAX(salary)        AS highest_salary
FROM employees;
```

---

## String Functions

```sql
-- Length and case
SELECT LENGTH('hello');                    -- 5
SELECT UPPER('hello');                     -- HELLO
SELECT LOWER('HELLO');                     -- hello

-- Trimming whitespace
SELECT TRIM('  hello  ');                  -- 'hello'
SELECT LTRIM('  hello');                   -- 'hello'
SELECT RTRIM('hello  ');                   -- 'hello'

-- Substring extraction
SELECT SUBSTRING('Hello World', 7, 5);    -- 'World'
SELECT LEFT('Hello', 3);                   -- 'Hel'
SELECT RIGHT('Hello', 3);                  -- 'llo'

-- Search and replace
SELECT LOCATE('lo', 'Hello');              -- 4
SELECT REPLACE('Hello World', 'World', 'MySQL'); -- 'Hello MySQL'

-- Concatenation
SELECT CONCAT('Hello', ' ', 'World');      -- 'Hello World'
SELECT CONCAT_WS(', ', 'Smith', 'Alice');  -- 'Smith, Alice'

-- Padding
SELECT LPAD('42', 5, '0');                 -- '00042'
SELECT RPAD('42', 5, '0');                 -- '42000'

-- Reverse
SELECT REVERSE('MySQL');                   -- 'LQSyM'
```

---

## Numeric Functions

```sql
SELECT ABS(-15);                -- 15
SELECT ROUND(3.14159, 2);       -- 3.14
SELECT FLOOR(3.9);              -- 3
SELECT CEIL(3.1);               -- 4
SELECT TRUNCATE(3.14159, 2);    -- 3.14 (no rounding)
SELECT MOD(10, 3);              -- 1
SELECT POWER(2, 8);             -- 256
SELECT SQRT(144);               -- 12
SELECT RAND();                  -- random float 0 ≤ x < 1
```

---

## Date & Time Functions

```sql
-- Current date and time
SELECT NOW();            -- '2024-06-15 14:30:00'
SELECT CURDATE();        -- '2024-06-15'
SELECT CURTIME();        -- '14:30:00'

-- Extract parts
SELECT YEAR('2024-06-15');       -- 2024
SELECT MONTH('2024-06-15');      -- 6
SELECT DAY('2024-06-15');        -- 15
SELECT HOUR(NOW());
SELECT MINUTE(NOW());
SELECT DAYNAME('2024-06-15');    -- 'Saturday'
SELECT MONTHNAME('2024-06-15');  -- 'June'

-- Arithmetic
SELECT DATE_ADD('2024-01-01', INTERVAL 3 MONTH);  -- '2024-04-01'
SELECT DATE_SUB('2024-06-15', INTERVAL 10 DAY);   -- '2024-06-05'
SELECT DATEDIFF('2024-12-31', '2024-01-01');       -- 365

-- Formatting
SELECT DATE_FORMAT(NOW(), '%d/%m/%Y %H:%i');       -- '15/06/2024 14:30'

-- Converting
SELECT STR_TO_DATE('15-06-2024', '%d-%m-%Y');     -- '2024-06-15'
```

---

## Conditional Expressions

### CASE

```sql
SELECT first_name,
       salary,
       CASE
           WHEN salary < 40000  THEN 'Junior'
           WHEN salary < 80000  THEN 'Mid-level'
           ELSE                      'Senior'
       END AS level
FROM employees;
```

### IF

```sql
SELECT first_name,
       IF(salary > 60000, 'High', 'Standard') AS pay_band
FROM employees;
```

### IFNULL / COALESCE

```sql
-- Return alternative if value is NULL
SELECT IFNULL(phone, 'N/A') AS contact FROM employees;

-- Return first non-NULL from a list
SELECT COALESCE(phone, mobile, email, 'N/A') AS contact FROM employees;
```

### NULLIF

```sql
-- Returns NULL if the two expressions are equal, otherwise returns the first
SELECT NULLIF(dept_id, 0) FROM employees;
```
