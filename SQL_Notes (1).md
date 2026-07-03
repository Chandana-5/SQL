# 📘 SQL Notes — Joins, Subqueries, EXISTS, INSERT, DELETE, MERGE, UPDATE

Sample tables used throughout:

**Students**
| id | name  | marks |
|----|-------|-------|
| 1  | Amit  | 70    |
| 2  | Neha  | 85    |
| 3  | Ravi  | 60    |

**Enrollments**
| id | student_id | course     |
|----|------------|------------|
| 1  | 1          | Math       |
| 2  | 1          | Physics    |
| 3  | 4          | Chemistry  |

> Note: student **4** doesn't exist in Students, and students **2, 3** have no enrollments — this mismatch is used to show join behavior below.

---

## 🔗 Joins

### 1. INNER JOIN
Returns only matching rows from both tables.
```sql
SELECT S.name, E.course
FROM Students S
INNER JOIN Enrollments E ON S.id = E.student_id;
```
**Result:** Only Amit (id=1) appears — matched in both tables.
| name | course  |
|------|---------|
| Amit | Math    |
| Amit | Physics |

---

### 2. LEFT JOIN
All rows from left table (Students) + matched rows from right (unmatched = NULL).
```sql
SELECT S.name, E.course
FROM Students S
LEFT JOIN Enrollments E ON S.id = E.student_id;
```
**Result:**
| name | course  |
|------|---------|
| Amit | Math    |
| Amit | Physics |
| Neha | NULL    |
| Ravi | NULL    |

---

### 3. RIGHT JOIN
All rows from right table (Enrollments) + matched rows from left.
```sql
SELECT S.name, E.course
FROM Students S
RIGHT JOIN Enrollments E ON S.id = E.student_id;
```
**Result:**
| name | course    |
|------|-----------|
| Amit | Math      |
| Amit | Physics   |
| NULL | Chemistry |

---

### 4. FULL JOIN
All rows from both tables; unmatched sides filled with NULL.
```sql
SELECT S.name, E.course
FROM Students S
FULL JOIN Enrollments E ON S.id = E.student_id;
```
**Result:**
| name | course    |
|------|-----------|
| Amit | Math      |
| Amit | Physics   |
| Neha | NULL      |
| Ravi | NULL      |
| NULL | Chemistry |

---

### 5. SELF JOIN
A table joined with itself — e.g. finding students with equal marks.
```sql
SELECT A.name AS student1, B.name AS student2, A.marks
FROM Students A
JOIN Students B ON A.marks = B.marks AND A.id <> B.id;
```
Useful for comparing rows within the same table (e.g. employees & managers stored in one table).

---

### 6. CROSS JOIN
Cartesian product — every row of A with every row of B.
```sql
SELECT S.name, C.course_name
FROM Students S
CROSS JOIN Courses C;
```
If Students has 3 rows and Courses has 2 rows → result has 3 × 2 = 6 rows.

---

## 🧩 Subqueries

### 1. Single-row subquery
Returns exactly one value — used with `=`, `>`, `<`, etc.
```sql
SELECT name FROM Students
WHERE marks > (SELECT AVG(marks) FROM Students);
```
→ Students scoring above the class average.

### 2. Multi-row subquery
Returns multiple values — used with `IN`, `ANY`, `ALL`.
```sql
SELECT name FROM Students
WHERE id IN (SELECT student_id FROM Enrollments);
```
→ Students who have at least one enrollment.

### 3. Correlated subquery
Refers to the outer query's columns; runs once per outer row.
```sql
SELECT name FROM Students S
WHERE marks > (
    SELECT AVG(marks) FROM Students S2 WHERE S2.id <> S.id
);
```
→ Students scoring above the average of everyone *else*.

### 4. Subquery in FROM (derived table)
```sql
SELECT dept, MAX(avg_marks)
FROM (
    SELECT dept, AVG(marks) AS avg_marks
    FROM Students
    GROUP BY dept
) AS DeptAvg
GROUP BY dept;
```

### 5. Scalar subquery in SELECT
```sql
SELECT name,
       (SELECT COUNT(*) FROM Enrollments E WHERE E.student_id = S.id) AS course_count
FROM Students S;
```

---

## ✅ EXISTS / ❌ NOT EXISTS

Checks whether a subquery returns any rows (boolean check, not actual data).

```sql
-- EXISTS: students who have at least one enrollment
SELECT name FROM Students S
WHERE EXISTS (SELECT 1 FROM Enrollments E WHERE E.student_id = S.id);
```
**Result:** Amit (has enrollments)

```sql
-- NOT EXISTS: students with no enrollments
SELECT name FROM Students S
WHERE NOT EXISTS (SELECT 1 FROM Enrollments E WHERE E.student_id = S.id);
```
**Result:** Neha, Ravi

---

## ➕ INSERT

```sql
-- Insert a single row
INSERT INTO Students (id, name, marks)
VALUES (4, 'Priya', 88);

-- Insert multiple rows at once
INSERT INTO Students (id, name, marks)
VALUES (5, 'Karan', 76), (6, 'Divya', 92);

-- Insert using a subquery (copy data from another table)
INSERT INTO ToppersBackup (id, name, marks)
SELECT id, name, marks FROM Students WHERE marks > 80;
```

---

## ➖ DELETE

```sql
-- Delete rows matching a condition
DELETE FROM Students
WHERE marks < 40;

-- Delete using a subquery
DELETE FROM Enrollments
WHERE student_id IN (SELECT id FROM Students WHERE marks < 40);

-- Delete all rows (structure remains)
DELETE FROM Students;
```
⚠️ Without `WHERE`, it deletes **all rows**.

---

## 🔄 UPDATE

```sql
-- Update a single row
UPDATE Students
SET marks = 95
WHERE id = 1;

-- Update multiple columns
UPDATE Students
SET marks = 60, name = 'Ravi Kumar'
WHERE id = 3;

-- Update using a subquery
UPDATE Students
SET marks = marks + 5
WHERE id IN (SELECT student_id FROM Enrollments WHERE course = 'Math');
```

---

## 🔀 MERGE

Combines `INSERT`, `UPDATE`, and `DELETE` into one statement by comparing source and target tables.

**NewStudents (source)**
| id | name | marks |
|----|------|-------|
| 1  | Amit | 90    |
| 7  | Sana | 60    |

```sql
MERGE INTO Students AS T
USING NewStudents AS S
ON T.id = S.id
WHEN MATCHED THEN
    UPDATE SET T.marks = S.marks
WHEN NOT MATCHED THEN
    INSERT (id, name, marks) VALUES (S.id, S.name, S.marks)
WHEN NOT MATCHED BY SOURCE THEN
    DELETE;
```

- **MATCHED** → Amit's marks updated to 90
- **NOT MATCHED** → Sana inserted as new row
- **NOT MATCHED BY SOURCE** → any student not present in NewStudents gets deleted (use carefully!)

---

### 📝 Quick Recap
| Concept | Purpose |
|---|---|
| INNER/LEFT/RIGHT/FULL JOIN | Combine matched/unmatched rows across tables |
| SELF JOIN | Compare rows within the same table |
| CROSS JOIN | Every combination of rows (Cartesian product) |
| Subquery | Query nested inside another query |
| EXISTS/NOT EXISTS | Check presence/absence of related rows |
| INSERT | Add data |
| DELETE | Remove data |
| UPDATE | Modify data |
| MERGE | Sync two tables (insert + update + delete) |
