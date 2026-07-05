# SQL Notes: Transactions, Identity, sp_rename, Delete & Truncate

---

## 1. Transactions (BEGIN, COMMIT, ROLLBACK, SAVEPOINT)

Group multiple SQL statements together — all succeed or all fail (no half-done changes).

```sql
BEGIN;  -- start transaction

UPDATE accounts SET balance = balance - 500 WHERE id = 1;
SAVEPOINT after_deduct;

UPDATE accounts SET balance = balance + 500 WHERE id = 2;

ROLLBACK TO after_deduct;  -- undo only the last step

COMMIT;  -- save everything permanently
```

- **BEGIN** — start the transaction
- **SAVEPOINT** — checkpoint to roll back to (partial undo)
- **ROLLBACK** — undo changes (fully, or to a savepoint)
- **COMMIT** — make changes permanent (no undo after this)

**Analogy:** Like a video game checkpoint system.
- BEGIN = start playing
- SAVEPOINT = save progress at a checkpoint
- ROLLBACK TO savepoint = go back to that checkpoint
- ROLLBACK (no savepoint) = go back to the very start
- COMMIT = "game saved for good" — no undo after this

---

## 2. IDENTITY

Auto-increments a column's value — you don't manually assign it.

```sql
CREATE TABLE students (
    id INT IDENTITY(1,1) PRIMARY KEY,  -- starts at 1, increases by 1
    name VARCHAR(50)
);

INSERT INTO students (name) VALUES ('Amit');  -- id auto = 1
```

To manually insert a specific id (bypass auto-increment temporarily):

```sql
SET IDENTITY_INSERT students ON;
INSERT INTO students (id, name) VALUES (10, 'Raj');
SET IDENTITY_INSERT students OFF;
```

**Notes:**
- Only one table per session can have `IDENTITY_INSERT` ON at a time (SQL Server).
- Always turn it back OFF after inserting.
- Common use case: migrating data and preserving original IDs.

---

## 3. sp_rename

Renames a table or column (SQL Server only).

```sql
-- Rename a table
EXEC sp_rename 'old_table_name', 'new_table_name';

-- Rename a column
EXEC sp_rename 'table_name.old_column', 'new_column', 'COLUMN';
```

**Example:**

```sql
EXEC sp_rename 'students', 'learners';
EXEC sp_rename 'learners.name', 'full_name', 'COLUMN';
```

---

## 4. DELETE vs TRUNCATE

| | DELETE | TRUNCATE |
|---|---|---|
| Removes | Specific rows (or all, with no WHERE) | All rows only |
| WHERE clause | Allowed | Not allowed |
| Speed | Slower (logs each row) | Faster (deallocates whole table) |
| Rollback | Can rollback (if in transaction) | Usually can't rollback (varies by DB) |
| Resets IDENTITY | No | Yes (id restarts from 1) |
| Triggers fired | Yes | No |

**Examples:**

```sql
DELETE FROM students WHERE id = 5;   -- removes one row
DELETE FROM students;                -- removes all rows (slow, logged)

TRUNCATE TABLE students;             -- removes all rows (fast, resets identity)
```

**Simple rule:** Need to delete *some* rows → `DELETE`. Need to wipe the *entire* table quickly → `TRUNCATE`.

---

*Notes compiled on July 5, 2026.*
