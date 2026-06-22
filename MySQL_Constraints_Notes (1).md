# 📘 MySQL Constraints & Queries — Study Notes

> **Topic:** SQL Constraints, UPDATE & DELETE Queries  
> **Date:** 22 June 2026  
> **Database Used:** MySQL Workbench

---

## 📌 What are Constraints?

Constraints are **rules applied to table columns** to control what data can be stored.  
They ensure **accuracy, validity, and integrity** of data in the database.

---

## 1️⃣ NOT NULL Constraint

Ensures a column **cannot have empty (NULL) values**.

### Syntax:
```sql
CREATE TABLE students (
    id INT,
    name VARCHAR(50) NOT NULL
);
```

### Example:
```sql
INSERT INTO students(id, name) VALUES (1, 'Rahul');   -- ✅ Works
INSERT INTO students(id, name) VALUES (2, NULL);       -- ❌ Error: Column cannot be null
```

### Key Points:
- Every row **must** have a value for this column
- Used for important fields like `name`, `email`, `phone`

---

## 2️⃣ NULL Constraint

Allows a column to **accept empty (NULL) values** (this is the default behavior).

### Syntax:
```sql
CREATE TABLE students (
    id INT,
    email VARCHAR(50) NULL   -- optional, NULL is default
);
```

### Example:
```sql
INSERT INTO students(id, email) VALUES (1, NULL);  -- ✅ Works fine
```

### Key Points:
- NULL means **no value / unknown**
- NULL is **not** the same as 0 or empty string `""`

---

## 3️⃣ UNIQUE Constraint

Ensures **all values in a column are different** — no duplicates allowed.

### Syntax:
```sql
CREATE TABLE students (
    id INT,
    email VARCHAR(50) UNIQUE
);
```

### Example:
```sql
INSERT INTO students VALUES (1, 'rahul@gmail.com');   -- ✅ Works
INSERT INTO students VALUES (2, 'rahul@gmail.com');   -- ❌ Error: Duplicate entry
```

### Key Points:
- Allows **NULL values** (multiple NULLs are allowed)
- Used for fields like `email`, `phone`, `username`
- A table can have **multiple UNIQUE columns**

---

## 4️⃣ DEFAULT Constraint

Sets a **default value** for a column if no value is provided during INSERT.

### Syntax:
```sql
CREATE TABLE students (
    id INT,
    city VARCHAR(50) DEFAULT 'Bangalore'
);
```

### Example:
```sql
INSERT INTO students(id) VALUES (1);
-- city will automatically be 'Bangalore'

INSERT INTO students(id, city) VALUES (2, 'Mysore');
-- city will be 'Mysore' (overrides default)
```

### Key Points:
- Default value is used **only when no value is given**
- Can be used with strings, numbers, dates

---

## 5️⃣ AUTO_INCREMENT Constraint

Automatically **generates a unique number** for each new row (usually for primary keys).

### Syntax:
```sql
CREATE TABLE students (
    student_id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50)
);
```

### Example:
```sql
INSERT INTO students(name) VALUES ('Akhil');   -- student_id = 1
INSERT INTO students(name) VALUES ('Riya');    -- student_id = 2
INSERT INTO students(name) VALUES ('Rahul');   -- student_id = 3
```

### Key Points:
- Starts from **1 by default**, increases by 1 each time
- You **don't need to insert** the id manually
- Usually combined with `PRIMARY KEY`

---

## 6️⃣ PRIMARY KEY Constraint

Uniquely identifies **each row** in a table.

### Syntax:
```sql
CREATE TABLE students (
    student_id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50)
);
```

### Key Points:
- Combination of **NOT NULL + UNIQUE**
- Only **one PRIMARY KEY** per table
- Often used with AUTO_INCREMENT

---

## 📝 UPDATE Query

Used to **modify existing data** in a table.

### Syntax:
```sql
UPDATE table_name
SET column_name = 'new_value'
WHERE condition;
```

### Example:
```sql
-- Update city of Rahul
UPDATE record
SET city = 'Mysore'
WHERE name = 'Rahul';
```

### Update Multiple Columns:
```sql
UPDATE record
SET city = 'Mysore', email = 'rahul@gmail.com'
WHERE name = 'Rahul';
```

### ⚠️ Safe Update Mode Error (Error 1175):
MySQL Workbench blocks UPDATE without primary key in WHERE clause.

**Fix:**
```sql
SET SQL_SAFE_UPDATES = 0;

UPDATE record
SET city = 'Mysore'
WHERE name = 'Rahul';

SET SQL_SAFE_UPDATES = 1;
```

---

## 🗑️ DELETE Query

Used to **remove rows** from a table.

### Syntax:
```sql
DELETE FROM table_name
WHERE condition;
```

### Example:
```sql
-- Delete a specific student
DELETE FROM students WHERE name = 'Rahul';

-- Delete all students from Mysore
DELETE FROM students WHERE city = 'Mysore';
```

### ⚠️ Delete ALL rows (use carefully!):
```sql
DELETE FROM students;
-- This deletes ALL rows but keeps the table structure
```

### Key Points:
- Always use **WHERE clause** or all rows will be deleted
- Cannot be undone easily — be careful!

---

## 🧪 Full Practice Example (Today's Code)

```sql
-- Step 1: Create Database
CREATE DATABASE record;

-- Step 2: Use Database
USE record;

-- Step 3: Create Table with Constraints
CREATE TABLE record (
    student_id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    email VARCHAR(50) UNIQUE
);

-- Step 4: Insert Records
INSERT INTO record(name, email) VALUES
    ('Akhil', 'akhil@gmail.com'),
    ('Riya', 'riya@gmail.com'),
    ('kiran', NULL),
    ('Neha', 'neha@gmail.com'),
    ('Rahul', 'rahul@gmail.com');

-- Step 5: Add New Column
ALTER TABLE record ADD city VARCHAR(50) DEFAULT 'Unknown';

-- Step 6: Update a Record
SET SQL_SAFE_UPDATES = 0;

UPDATE record
SET city = 'Mysore'
WHERE name = 'Rahul';

SET SQL_SAFE_UPDATES = 1;

-- Step 7: View All Records
SELECT * FROM record;

-- Step 8: Delete a Record
DELETE FROM record WHERE name = 'Riya';
```

---

## 📊 Constraints Summary Table

| Constraint | Purpose | Allows NULL? | Allows Duplicates? |
|---|---|---|---|
| NOT NULL | Must have a value | ❌ No | ✅ Yes |
| NULL | Value is optional | ✅ Yes | ✅ Yes |
| UNIQUE | No duplicate values | ✅ Yes | ❌ No |
| DEFAULT | Auto fills a value | ✅ Yes | ✅ Yes |
| AUTO_INCREMENT | Auto generates number | ❌ No | ❌ No |
| PRIMARY KEY | Unique row identifier | ❌ No | ❌ No |

---

## 💡 Tips to Remember

- Always use `WHERE` in UPDATE and DELETE — or all rows get affected!
- `PRIMARY KEY = NOT NULL + UNIQUE`
- `AUTO_INCREMENT` only works with integer columns
- `NULL` is not zero — it means **no value**
- Use `SET SQL_SAFE_UPDATES = 0` to fix Error 1175 in Workbench

---

*Notes prepared on 22 June 2026 | MySQL Workbench | Samsung S24*
