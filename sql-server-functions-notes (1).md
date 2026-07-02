# SQL Server Functions — Quick Reference

Short notes covering Mathematical, String, Date & Time functions, and Data Conversion in SQL Server.
Examples use a sample `EMP` table: `EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO`

---

## 1. Mathematical Functions

| Function | Purpose | Example | Output |
|---|---|---|---|
| `ABS(n)` | Returns absolute (positive) value | `SELECT ABS(-800);` | `800` |
| `SQRT(n)` | Returns square root | `SELECT SQRT(SAL) FROM EMP;` | e.g. `28.28` for SAL=800 |
| `ROUND(n, d)` | Rounds to `d` decimal places (negative `d` rounds to tens/hundreds) | `SELECT ROUND(SAL,-2) FROM EMP;` | `800` |
| `CEILING(n)` | Rounds up to nearest integer | `SELECT CEILING(4.2);` | `5` |
| `FLOOR(n)` | Rounds down to nearest integer | `SELECT FLOOR(4.8);` | `4` |
| `POWER(n, p)` | Raises `n` to power `p` | `SELECT POWER(2,3);` | `8` |
| `MAX(col)` | Highest value in a column (aggregate) | `SELECT MAX(SAL) FROM EMP;` | highest salary |
| `MIN(col)` | Lowest value in a column (aggregate) | `SELECT MIN(SAL) FROM EMP;` | lowest salary |
| `SUM(col)` | Total of a column (aggregate) | `SELECT SUM(SAL) FROM EMP;` | total salary |
| `AVG(col)` | Average of a column (aggregate) | `SELECT AVG(SAL) FROM EMP;` | average salary |

**Arithmetic operators:** `+`, `-`, `*`, `/`, `%` (modulus/remainder)
- `SELECT 5/2;` → `2` (integer division)
- `SELECT 5/2.0;` → `2.500000` (decimal division)
- `SELECT SAL % 300 FROM EMP;` → remainder when SAL is divided by 300

---

## 2. String Functions

| Function | Purpose | Example | Output |
|---|---|---|---|
| `UPPER(str)` | Converts to uppercase | `SELECT UPPER(ENAME) FROM EMP;` | `SMITH` |
| `LOWER(str)` | Converts to lowercase | `SELECT LOWER(ENAME) FROM EMP;` | `smith` |
| `LEN(str)` | Returns length of string | `SELECT LEN(ENAME) FROM EMP;` | `5` (for SMITH) |
| `SUBSTRING(str, start, length)` | Extracts part of a string | `SELECT SUBSTRING(ENAME,1,3) FROM EMP;` | `SMI` |
| `CHARINDEX(substr, str)` | Finds position of a substring (0 if not found) | `SELECT CHARINDEX('I','SMITH');` | `4` |
| `LTRIM(str)` | Removes leading spaces | `SELECT LTRIM('  SQL');` | `SQL` |
| `RTRIM(str)` | Removes trailing spaces | `SELECT RTRIM('SQL  ');` | `SQL` |
| `REPLACE(str, old, new)` | Replaces occurrences of a substring | `SELECT REPLACE('SQL2005','2005','2026');` | `SQL2026` |
| `CONCAT(str1, str2, ...)` | Joins strings together | `SELECT CONCAT(ENAME,' - ',JOB) FROM EMP;` | `SMITH - CLERK` |
| `+` (concatenation) | Joins strings (SQL Server specific) | `SELECT ENAME + ' works as ' + JOB FROM EMP;` | `SMITH works as CLERK` |

---

## 3. Date & Time Functions

| Function | Purpose | Example | Output |
|---|---|---|---|
| `GETDATE()` | Returns current date & time | `SELECT GETDATE();` | e.g. `2026-07-02 18:50:00` |
| `DATEPART(part, date)` | Extracts a specific part of a date | `SELECT DATEPART(YEAR, HIREDATE) FROM EMP;` | `1980` |
| `DATENAME(part, date)` | Returns the name (string) of a date part | `SELECT DATENAME(MONTH, HIREDATE) FROM EMP;` | `December` |
| `DATEDIFF(part, date1, date2)` | Difference between two dates | `SELECT DATEDIFF(YEAR, HIREDATE, GETDATE()) FROM EMP;` | years of service |
| `DATEADD(part, n, date)` | Adds/subtracts time from a date | `SELECT DATEADD(YEAR,1,HIREDATE) FROM EMP;` | date + 1 year |
| `DAY(date)` | Returns day number | `SELECT DAY(HIREDATE) FROM EMP;` | `17` |
| `MONTH(date)` | Returns month number | `SELECT MONTH(HIREDATE) FROM EMP;` | `12` |
| `YEAR(date)` | Returns year | `SELECT YEAR(HIREDATE) FROM EMP;` | `1980` |

**Common filtering example:**
```sql
-- Find employees hired in February (any year)
SELECT ENAME FROM EMP WHERE DATEPART(MONTH, HIREDATE) = 2;
```

---

## 4. Data Conversion

| Function | Purpose | Example | Output |
|---|---|---|---|
| `CAST(value AS datatype)` | Converts value to a specified data type (standard SQL, no formatting styles) | `SELECT CAST(SAL AS INT) FROM EMP;` | truncated integer |
| `CONVERT(datatype, value, style)` | Converts with optional format style (SQL Server only) | `SELECT CONVERT(VARCHAR(20), HIREDATE, 103) FROM EMP;` | `17/12/1980` |

**Common CONVERT date style codes:**

| Style Code | Format |
|---|---|
| 101 | MM/DD/YYYY |
| 103 | DD/MM/YYYY |
| 105 | DD-MM-YYYY |
| 120 | YYYY-MM-DD HH:MI:SS |

**Examples:**
```sql
-- decimal to int
SELECT CAST(SAL AS INT) FROM EMP;

-- string to date
SELECT CONVERT(DATE, '2026-07-02') AS Converted_Date;

-- date to formatted string for reports
SELECT CONVERT(VARCHAR(20), HIREDATE, 103) AS Formatted_Hiredate FROM EMP;
```

**CAST vs CONVERT — when to use which:**
- Use `CAST` for simple, portable conversions (works across most database systems).
- Use `CONVERT` in SQL Server specifically when you need control over **date/time display format**.

---

### Notes
- SQL Server strings are **1-indexed** (first character = position 1).
- `/` between two integers performs **integer division** (drops decimals); use a decimal operand (e.g. `2.0`) to get a decimal result.
- Aggregate functions (`SUM`, `AVG`, `MAX`, `MIN`, `COUNT`) work on a *set* of rows — pair with `GROUP BY` when summarizing by category.
