# DCL – Data Control Language

DCL commands manage **permissions** — who can access what, and what they can do with it.

---

## GRANT

Gives a user (or role) specific privileges on a database object.

### Syntax

```sql
GRANT privilege_list
ON object
TO 'user'@'host'
[WITH GRANT OPTION];
```

### Common privileges

| Privilege | What it allows |
|-----------|---------------|
| `ALL PRIVILEGES` | Every available privilege |
| `SELECT` | Read rows |
| `INSERT` | Add rows |
| `UPDATE` | Modify rows |
| `DELETE` | Remove rows |
| `CREATE` | Create databases / tables |
| `DROP` | Drop databases / tables |
| `ALTER` | Modify table structure |
| `INDEX` | Create / drop indexes |
| `EXECUTE` | Call stored procedures / functions |
| `REFERENCES` | Create foreign keys |
| `GRANT OPTION` | Pass granted privileges on to others |

### Examples

```sql
-- Grant SELECT on a specific table
GRANT SELECT ON shop.products TO 'reporting_user'@'localhost';

-- Grant multiple privileges
GRANT SELECT, INSERT, UPDATE ON shop.* TO 'app_user'@'%';

-- Grant all privileges on a database
GRANT ALL PRIVILEGES ON shop.* TO 'admin'@'localhost';

-- Grant global privileges (all databases)
GRANT ALL PRIVILEGES ON *.* TO 'superadmin'@'localhost'
WITH GRANT OPTION;

-- Apply changes immediately (usually done after GRANT/REVOKE)
FLUSH PRIVILEGES;
```

---

## REVOKE

Removes previously granted privileges.

### Syntax

```sql
REVOKE privilege_list
ON object
FROM 'user'@'host';
```

### Examples

```sql
-- Remove INSERT on a single table
REVOKE INSERT ON shop.products FROM 'app_user'@'%';

-- Remove all privileges on a database
REVOKE ALL PRIVILEGES ON shop.* FROM 'app_user'@'%';

-- Remove the ability to pass privileges to others
REVOKE GRANT OPTION ON *.* FROM 'admin'@'localhost';
```

---

## SHOW GRANTS

Displays the privileges assigned to a user.

```sql
-- Privileges for the current user
SHOW GRANTS;

-- Privileges for a specific user
SHOW GRANTS FOR 'app_user'@'%';
```

---

## Notes

- Changes made by `GRANT` and `REVOKE` take effect immediately in MySQL 8.  
  On older versions you may need `FLUSH PRIVILEGES` after manual edits to the `mysql.user` table.
- The `'user'@'host'` combination is unique — `'alice'@'localhost'` and `'alice'@'%'` are different accounts.
- Wildcard `%` in the host means "any host".
