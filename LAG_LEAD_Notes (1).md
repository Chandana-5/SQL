# SQL Window Functions: LAG & LEAD

Notes on `LAG()` and `LEAD()` — used to access values from other rows without a self-join.

---

## Why they exist

Normally, a row in SQL only "knows" its own values. If you need to compare a row to the **previous** or **next** row (e.g., yesterday vs today), you'd typically need a self-join.

`LAG` and `LEAD` solve this in a single pass — no join required.

---

## LAG() — look backward

Returns a value from a **previous** row.

### Syntax

```sql
LAG(column_name, offset, default_value) OVER (
    [PARTITION BY partition_column]
    ORDER BY sort_column
)
```

- `column_name` — the column to pull from a previous row
- `offset` — how many rows back (default: 1)
- `default_value` — value to use if there's no previous row (default: NULL)
- `PARTITION BY` — optional, restarts the lookback per group
- `ORDER BY` — required, defines row order

### Example

```sql
SELECT day, value,
       LAG(value) OVER (ORDER BY day) AS prev_value
FROM sales;
```

| day | value | prev_value |
|-----|-------|------------|
| 1   | 10    | NULL       |
| 2   | 15    | 10         |
| 3   | 7     | 15         |

---

## LEAD() — look forward

Returns a value from a **following** row. Same syntax as `LAG`, just looks ahead instead of behind.

### Syntax

```sql
LEAD(column_name, offset, default_value) OVER (
    [PARTITION BY partition_column]
    ORDER BY sort_column
)
```

### Example

```sql
SELECT day, value,
       LEAD(value) OVER (ORDER BY day) AS next_value
FROM sales;
```

| day | value | next_value |
|-----|-------|------------|
| 1   | 10    | 15         |
| 2   | 15    | 7          |
| 3   | 7     | NULL       |

---

## Real problems solved with LAG/LEAD

### 1. Rising Temperature (LeetCode)

Compare each day's temperature to the previous day.

```sql
SELECT id
FROM (
    SELECT id, recordDate, temperature,
           LAG(temperature) OVER (ORDER BY recordDate) AS prev_temp,
           LAG(recordDate) OVER (ORDER BY recordDate) AS prev_date
    FROM Weather
) t
WHERE temperature > prev_temp
AND DATEDIFF(recordDate, prev_date) = 1;
```

`LAG(temperature)` pulls yesterday's temperature into today's row, so the comparison happens without a self-join.

### 2. Consecutive Numbers (LeetCode)

Find numbers that repeat 3+ times in a row.

```sql
SELECT DISTINCT num AS ConsecutiveNums
FROM (
    SELECT num,
           LAG(num, 1) OVER (ORDER BY id) AS prev1,
           LAG(num, 2) OVER (ORDER BY id) AS prev2
    FROM Logs
) t
WHERE num = prev1 AND num = prev2;
```

`LAG(num, 1)` and `LAG(num, 2)` check the previous two rows at once — if all three values match, it's a streak of 3.

---

## Key takeaways

- `LAG` = look backward, `LEAD` = look forward
- Both require `ORDER BY` inside `OVER()` — without it, "previous/next row" has no defined meaning
- `PARTITION BY` resets the lookback/lookahead per group (e.g., per employee, per customer)
- These replace many self-join patterns with a single, more readable pass over the data
- Common use cases: day-over-day comparisons, detecting streaks/consecutive values, calculating differences between rows
