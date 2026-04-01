# DDL – Data Definition Language

DDL commands define and manage the **structure** of database objects (databases, tables, indexes, views, etc.).  
These commands are auto-committed — they cannot be rolled back.

---

## CREATE

Creates a new database object.

### Create a database

```sql
CREATE DATABASE shop;

-- Avoid an error if it already exists
CREATE DATABASE IF NOT EXISTS shop;

-- Specify character set and collation
CREATE DATABASE shop
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_unicode_ci;
```

### Create a table

```sql
CREATE TABLE employees (
    id          INT           NOT NULL AUTO_INCREMENT,
    first_name  VARCHAR(50)   NOT NULL,
    last_name   VARCHAR(50)   NOT NULL,
    email       VARCHAR(100)  UNIQUE,
    salary      DECIMAL(10,2) DEFAULT 0.00,
    hire_date   DATE,
    dept_id     INT,
    PRIMARY KEY (id),
    FOREIGN KEY (dept_id) REFERENCES departments(id)
);
```

### Create a table from another table

```sql
-- Copy structure AND data
CREATE TABLE employees_backup AS
    SELECT * FROM employees;

-- Copy structure only (no rows)
CREATE TABLE employees_backup AS
    SELECT * FROM employees WHERE 1 = 0;
```

### Create an index

```sql
-- Regular index (speeds up queries on last_name)
CREATE INDEX idx_last_name ON employees (last_name);

-- Unique index (enforces uniqueness)
CREATE UNIQUE INDEX idx_email ON employees (email);

-- Composite index
CREATE INDEX idx_name ON employees (last_name, first_name);
```

### Create a view

```sql
CREATE VIEW engineering_staff AS
    SELECT id, first_name, last_name, email
    FROM   employees
    WHERE  dept_id = (SELECT id FROM departments WHERE name = 'Engineering');
```

---

## ALTER

Modifies an existing database object.

### Add a column

```sql
ALTER TABLE employees ADD COLUMN phone VARCHAR(20);

-- Add at a specific position
ALTER TABLE employees ADD COLUMN phone VARCHAR(20) AFTER email;
ALTER TABLE employees ADD COLUMN code  INT          FIRST;
```

### Modify a column (change type or constraints)

```sql
ALTER TABLE employees MODIFY COLUMN salary DECIMAL(12,2) NOT NULL;
```

### Rename a column

```sql
ALTER TABLE employees RENAME COLUMN phone TO mobile;

-- Older syntax (also changes type)
ALTER TABLE employees CHANGE phone mobile VARCHAR(20);
```

### Drop a column

```sql
ALTER TABLE employees DROP COLUMN mobile;
```

### Add / drop a primary key

```sql
ALTER TABLE employees ADD PRIMARY KEY (id);
ALTER TABLE employees DROP PRIMARY KEY;
```

### Add / drop a foreign key

```sql
ALTER TABLE employees
    ADD CONSTRAINT fk_dept
    FOREIGN KEY (dept_id) REFERENCES departments(id);

ALTER TABLE employees DROP FOREIGN KEY fk_dept;
```

### Add / drop an index

```sql
ALTER TABLE employees ADD INDEX idx_last_name (last_name);
ALTER TABLE employees DROP INDEX idx_last_name;
```

### Rename a table

```sql
ALTER TABLE employees RENAME TO staff;
```

---

## DROP

Permanently deletes a database object and all its data.

```sql
-- Drop a table
DROP TABLE employees;
DROP TABLE IF EXISTS employees;          -- no error if missing

-- Drop a database (deletes ALL tables inside it)
DROP DATABASE shop;
DROP DATABASE IF EXISTS shop;

-- Drop an index
DROP INDEX idx_last_name ON employees;

-- Drop a view
DROP VIEW engineering_staff;
```

> **Warning:** `DROP` cannot be undone. Always back up data first.

---

## TRUNCATE

Removes **all rows** from a table but keeps the table structure intact.  
Faster than `DELETE` because it does not log individual row deletions.

```sql
TRUNCATE TABLE employees;

-- Equivalent to (but faster than):
DELETE FROM employees;
```

Key differences from `DELETE`:
| | `TRUNCATE` | `DELETE` |
|---|---|---|
| Removes all rows | ✅ | ✅ (without WHERE) |
| Resets AUTO_INCREMENT | ✅ | ❌ |
| Can use WHERE | ❌ | ✅ |
| Can be rolled back | ❌ | ✅ |
| Fires DELETE triggers | ❌ | ✅ |

---

## RENAME

Renames one or more tables in a single atomic operation.

```sql
RENAME TABLE employees TO staff;

-- Rename multiple tables at once
RENAME TABLE
    employees    TO staff,
    departments  TO divisions;
```
