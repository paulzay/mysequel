# Administration

These commands and utilities help you explore, diagnose, and manage a MySQL server.

---

## USE

Selects a database as the default for the current session.

```sql
USE shop;
```

---

## SHOW

A family of commands for listing server metadata.

```sql
-- List all databases
SHOW DATABASES;
SHOW SCHEMAS;              -- synonym

-- List tables in the current database
SHOW TABLES;
SHOW TABLES FROM shop;     -- from a specific database
SHOW FULL TABLES;          -- also shows table type (BASE TABLE / VIEW)

-- List columns of a table
SHOW COLUMNS FROM employees;
SHOW FULL COLUMNS FROM employees;   -- includes collation, privileges, comment

-- List indexes on a table
SHOW INDEX FROM employees;

-- List running processes / active connections
SHOW PROCESSLIST;
SHOW FULL PROCESSLIST;

-- Show current server variables
SHOW VARIABLES;
SHOW VARIABLES LIKE 'max_connections';
SHOW VARIABLES LIKE '%timeout%';

-- Show server status counters
SHOW STATUS;
SHOW STATUS LIKE 'Threads%';

-- Show the CREATE statement for an object
SHOW CREATE TABLE employees;
SHOW CREATE DATABASE shop;
SHOW CREATE VIEW engineering_staff;

-- Show active warnings / errors from the last statement
SHOW WARNINGS;
SHOW ERRORS;

-- Show available storage engines
SHOW ENGINES;

-- Show binary log files
SHOW BINARY LOGS;
```

---

## DESCRIBE / DESC

Shows the structure (columns, types, keys) of a table.  
`DESCRIBE` and `DESC` are identical.

```sql
DESCRIBE employees;
DESC employees;

-- Describe a specific column
DESCRIBE employees salary;
```

Sample output:

| Field | Type | Null | Key | Default | Extra |
|-------|------|------|-----|---------|-------|
| id | int | NO | PRI | NULL | auto_increment |
| first_name | varchar(50) | NO | | NULL | |
| salary | decimal(10,2) | YES | | 0.00 | |

---

## EXPLAIN / EXPLAIN ANALYZE

Shows the **query execution plan** — how MySQL will retrieve the data.  
Use this to find slow queries and missing indexes.

```sql
-- Basic execution plan
EXPLAIN SELECT * FROM employees WHERE dept_id = 3;

-- Verbose format
EXPLAIN FORMAT=JSON SELECT * FROM employees WHERE dept_id = 3;

-- Actually run the query and show real execution stats (MySQL 8.0.18+)
EXPLAIN ANALYZE SELECT * FROM employees WHERE dept_id = 3;
```

Key columns in the output:

| Column | Meaning |
|--------|---------|
| `type` | Join type (`ALL` = full scan — slow; `ref`, `eq_ref`, `const` = fast) |
| `key` | Index used (NULL means no index) |
| `rows` | Estimated rows examined |
| `Extra` | Additional notes (`Using index`, `Using filesort`, etc.) |

---

## KILL

Terminates a running query or connection.

```sql
-- Find the process ID first
SHOW PROCESSLIST;

-- Kill it
KILL 42;          -- terminates connection 42
KILL QUERY 42;    -- kills only the current query, keeps the connection open
```

---

## mysqldump (command-line utility)

Back up and restore databases from the terminal (not inside the MySQL prompt).

```bash
# Dump a single database
mysqldump -u root -p shop > shop_backup.sql

# Dump specific tables
mysqldump -u root -p shop employees departments > tables_backup.sql

# Dump all databases
mysqldump -u root -p --all-databases > all_databases.sql

# Dump without data (structure only)
mysqldump -u root -p --no-data shop > shop_structure.sql

# Compress the output
mysqldump -u root -p shop | gzip > shop_backup.sql.gz

# Restore from a dump file
mysql -u root -p shop < shop_backup.sql
```

---

## mysqlcheck (command-line utility)

Checks and repairs tables.

```bash
# Check all tables in a database
mysqlcheck -u root -p shop

# Repair all tables
mysqlcheck -u root -p --repair shop

# Optimize all tables (defragment)
mysqlcheck -u root -p --optimize shop

# Check all databases
mysqlcheck -u root -p --all-databases
```

---

## CHECK TABLE / REPAIR TABLE / OPTIMIZE TABLE

Run from inside the MySQL prompt.

```sql
-- Check a table for errors
CHECK TABLE employees;

-- Repair a corrupted table (MyISAM / ARCHIVE only)
REPAIR TABLE employees;

-- Defragment a table and reclaim unused space
OPTIMIZE TABLE employees;

-- Analyze key distributions (helps the optimizer pick the right index)
ANALYZE TABLE employees;
```

---

## SET (session / global variables)

```sql
-- Change a variable for the current session only
SET SESSION sql_mode = 'STRICT_TRANS_TABLES';
SET @@session.time_zone = '+05:30';

-- Change a variable globally (takes effect for new connections)
SET GLOBAL max_connections = 200;
SET @@global.max_connections = 200;

-- Check a variable
SELECT @@max_connections;
SHOW VARIABLES LIKE 'max_connections';
```

---

## FLUSH

Forces MySQL to reload internal caches.

```sql
FLUSH PRIVILEGES;   -- reload the grant tables (after manual edits to mysql.user)
FLUSH TABLES;       -- close all open tables
FLUSH LOGS;         -- rotate log files
FLUSH STATUS;       -- reset status counters
```
