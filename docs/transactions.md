# TCL – Transaction Control Language

Transactions group one or more SQL statements into a single, all-or-nothing unit of work.  
If any step fails you can roll back all changes; if every step succeeds you commit them permanently.

---

## Key concepts

| Property | Meaning |
|----------|---------|
| **Atomicity** | All statements in a transaction succeed or all are rolled back |
| **Consistency** | The database moves from one valid state to another |
| **Isolation** | Concurrent transactions do not interfere with each other |
| **Durability** | Committed changes survive system failures |

By default, MySQL runs in **auto-commit** mode — every statement is committed immediately.  
To use explicit transactions, either disable auto-commit or wrap statements with `START TRANSACTION`.

---

## START TRANSACTION / BEGIN

Marks the beginning of a transaction (disables auto-commit for that block).

```sql
START TRANSACTION;
-- or equivalently:
BEGIN;
```

---

## COMMIT

Permanently saves all changes made since the last `START TRANSACTION`.

```sql
START TRANSACTION;

UPDATE accounts SET balance = balance - 500 WHERE id = 1;
UPDATE accounts SET balance = balance + 500 WHERE id = 2;

COMMIT;  -- both updates are now permanent
```

---

## ROLLBACK

Undoes all changes made since the last `START TRANSACTION` (or the last `SAVEPOINT`).

```sql
START TRANSACTION;

UPDATE accounts SET balance = balance - 500 WHERE id = 1;
-- Something went wrong...
ROLLBACK;  -- balance is restored to its original value
```

---

## SAVEPOINT

Creates a named checkpoint inside a transaction.  
You can roll back to a savepoint without undoing the entire transaction.

```sql
START TRANSACTION;

INSERT INTO orders (customer_id, total) VALUES (10, 250.00);

SAVEPOINT after_order;

INSERT INTO order_items (order_id, product_id, qty) VALUES (LAST_INSERT_ID(), 5, 2);
-- Oops, wrong product — roll back only the items insert
ROLLBACK TO SAVEPOINT after_order;

-- Retry with the correct product
INSERT INTO order_items (order_id, product_id, qty) VALUES (LAST_INSERT_ID(), 7, 2);

COMMIT;
```

### RELEASE SAVEPOINT

Removes a named savepoint (you can no longer roll back to it).

```sql
RELEASE SAVEPOINT after_order;
```

---

## SET autocommit

```sql
-- Disable auto-commit for the current session
SET autocommit = 0;

-- Re-enable auto-commit
SET autocommit = 1;

-- Check current setting
SELECT @@autocommit;
```

---

## Practical example — bank transfer

```sql
START TRANSACTION;

UPDATE accounts SET balance = balance - 200 WHERE id = 1;

-- Check the sender has sufficient funds
SELECT balance INTO @bal FROM accounts WHERE id = 1;

IF @bal < 0 THEN
    ROLLBACK;
ELSE
    UPDATE accounts SET balance = balance + 200 WHERE id = 2;
    COMMIT;
END IF;
```

> **Note:** DDL statements (`CREATE`, `ALTER`, `DROP`, `TRUNCATE`) cause an implicit `COMMIT` and cannot be rolled back.
