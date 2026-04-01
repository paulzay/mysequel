# User Management

These commands create, modify, and remove MySQL user accounts.

---

## CREATE USER

Creates a new MySQL user account.

```sql
-- Basic creation (password required at login)
CREATE USER 'alice'@'localhost' IDENTIFIED BY 'SecurePass1!';

-- Allow connections from any host
CREATE USER 'bob'@'%' IDENTIFIED BY 'SecurePass2!';

-- Avoid an error if the user already exists
CREATE USER IF NOT EXISTS 'carol'@'localhost' IDENTIFIED BY 'SecurePass3!';

-- Require the user to change their password on first login
CREATE USER 'temp_user'@'localhost'
    IDENTIFIED BY 'TempPass!'
    PASSWORD EXPIRE;

-- Account that never expires
CREATE USER 'service'@'localhost'
    IDENTIFIED BY 'ServicePass!'
    PASSWORD EXPIRE NEVER;
```

---

## DROP USER

Permanently deletes a user account and all its privileges.

```sql
DROP USER 'alice'@'localhost';
DROP USER IF EXISTS 'alice'@'localhost';   -- no error if missing

-- Drop multiple users at once
DROP USER 'bob'@'%', 'carol'@'localhost';
```

---

## ALTER USER / SET PASSWORD

Changes account properties such as the password.

```sql
-- Change your own password (MySQL 8+)
ALTER USER USER() IDENTIFIED BY 'NewPass!';

-- Change another user's password
ALTER USER 'alice'@'localhost' IDENTIFIED BY 'NewPass!';

-- Force password expiry (user must change on next login)
ALTER USER 'alice'@'localhost' PASSWORD EXPIRE;

-- Lock / unlock an account
ALTER USER 'alice'@'localhost' ACCOUNT LOCK;
ALTER USER 'alice'@'localhost' ACCOUNT UNLOCK;

-- Legacy SET PASSWORD syntax (still works in MySQL 8)
SET PASSWORD FOR 'alice'@'localhost' = 'NewPass!';
```

---

## SHOW GRANTS

Displays the privileges of a user (see also [data-control.md](data-control.md)).

```sql
-- Current user
SHOW GRANTS;

-- Specific user
SHOW GRANTS FOR 'alice'@'localhost';
```

---

## SHOW / SELECT user accounts

```sql
-- List all users
SELECT user, host, account_locked, password_expired
FROM mysql.user;

-- Current logged-in user
SELECT USER();
SELECT CURRENT_USER();
```

---

## RENAME USER

Renames an existing user without changing privileges.

```sql
RENAME USER 'alice'@'localhost' TO 'alicia'@'localhost';
```

---

## Typical workflow — create a restricted application user

```sql
-- 1. Create the user
CREATE USER 'app'@'%' IDENTIFIED BY 'AppSecure99!';

-- 2. Grant only what the application needs
GRANT SELECT, INSERT, UPDATE, DELETE ON myapp.* TO 'app'@'%';

-- 3. Verify
SHOW GRANTS FOR 'app'@'%';

-- 4. (If needed) revoke a privilege later
REVOKE DELETE ON myapp.* FROM 'app'@'%';

-- 5. Remove the user when no longer needed
DROP USER 'app'@'%';
```
