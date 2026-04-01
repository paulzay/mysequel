# mysequel

A reference guide for learning MySQL commands — what they are, what they do, and how to use them.

## Table of Contents

| File | Topic |
|------|-------|
| [docs/data-definition.md](docs/data-definition.md) | **DDL** – `CREATE`, `ALTER`, `DROP`, `TRUNCATE`, `RENAME` |
| [docs/data-manipulation.md](docs/data-manipulation.md) | **DML** – `SELECT`, `INSERT`, `UPDATE`, `DELETE` |
| [docs/clauses-and-functions.md](docs/clauses-and-functions.md) | **Clauses & Functions** – `WHERE`, `JOIN`, `GROUP BY`, `ORDER BY`, `LIMIT`, aggregate & built-in functions |
| [docs/transactions.md](docs/transactions.md) | **TCL** – `COMMIT`, `ROLLBACK`, `SAVEPOINT` |
| [docs/data-control.md](docs/data-control.md) | **DCL** – `GRANT`, `REVOKE` |
| [docs/user-management.md](docs/user-management.md) | **User Management** – `CREATE USER`, `DROP USER`, `SET PASSWORD`, `SHOW GRANTS` |
| [docs/administration.md](docs/administration.md) | **Administration** – `SHOW`, `DESCRIBE`, `USE`, `EXPLAIN`, `mysqldump` |

## Quick-start cheat sheet

```sql
-- Connect to MySQL
mysql -u root -p

-- Show all databases
SHOW DATABASES;

-- Select a database to work with
USE my_database;

-- Show all tables in the current database
SHOW TABLES;

-- Describe the structure of a table
DESCRIBE employees;

-- Run a basic query
SELECT * FROM employees WHERE department = 'Engineering';
```

Browse the `docs/` folder for detailed explanations and examples of every command category.