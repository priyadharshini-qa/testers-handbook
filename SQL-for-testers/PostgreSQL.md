# SQL for Testers 🧪

A practical reference for QA engineers and testers working with  PostgreSQL databases.

---

## Table of Contents

- [Basic SELECT Queries](#basic-select-queries)
- [Filtering Data](#filtering-data)
- [Sorting & Limiting](#sorting--limiting)
- [Aggregations & Grouping](#aggregations--grouping)
- [JOINs](#joins)
- [Subqueries](#subqueries)
- [Data Validation Queries](#data-validation-queries)
- [Date & Time](#date--time)
- [String Functions](#string-functions)
- [NULL Handling](#null-handling)
- [Useful Test Scenarios](#useful-test-scenarios)

---

## Basic SELECT Queries

```sql
-- Select all columns from a table
SELECT * FROM users;

-- Select specific columns
SELECT id, email, created_at FROM users;

-- Select with a column alias
SELECT id AS user_id, email AS user_email FROM users;

-- Count all rows in a table
SELECT COUNT(*) FROM orders;
```

---

## Filtering Data

```sql
-- Equality
SELECT * FROM users WHERE status = 'active';

-- Not equal
SELECT * FROM orders WHERE status <> 'cancelled';

-- Multiple conditions (AND / OR)
SELECT * FROM orders WHERE status = 'pending' AND amount > 100;
SELECT * FROM users WHERE role = 'admin' OR role = 'superuser';

-- IN list
SELECT * FROM products WHERE category IN ('electronics', 'clothing', 'books');

-- NOT IN
SELECT * FROM users WHERE id NOT IN (1, 2, 3);

-- Range (BETWEEN is inclusive)
SELECT * FROM orders WHERE amount BETWEEN 50 AND 200;

-- Pattern matching
SELECT * FROM users WHERE email LIKE '%@gmail.com';
SELECT * FROM users WHERE name LIKE 'John%';   -- starts with
SELECT * FROM users WHERE name LIKE '%son';    -- ends with
SELECT * FROM users WHERE name LIKE '%ar%';    -- contains
```

---

## Sorting & Limiting

```sql
-- Order ascending (default)
SELECT * FROM users ORDER BY created_at ASC;

-- Order descending
SELECT * FROM orders ORDER BY created_at DESC;

-- Limit results
SELECT * FROM orders LIMIT 10;

-- Offset (pagination)
SELECT * FROM orders LIMIT 10 OFFSET 20;  -- page 3

-- Get the most recent record
SELECT * FROM orders ORDER BY created_at DESC LIMIT 1;
```

---

## Aggregations & Grouping

```sql
-- Count, sum, average, min, max
SELECT COUNT(*) AS total_orders FROM orders;
SELECT SUM(amount) AS total_revenue FROM orders WHERE status = 'completed';
SELECT AVG(amount) AS avg_order_value FROM orders;
SELECT MIN(amount), MAX(amount) FROM orders;

-- Group by
SELECT status, COUNT(*) AS count FROM orders GROUP BY status;

-- Group by with HAVING (filter after aggregation)
SELECT user_id, COUNT(*) AS order_count
FROM orders
GROUP BY user_id
HAVING COUNT(*) > 5;

-- Count distinct values
SELECT COUNT(DISTINCT email) FROM users;
```

---

## JOINs

```sql
-- INNER JOIN — only matching rows in both tables
SELECT u.id, u.email, o.id AS order_id, o.amount
FROM users u
INNER JOIN orders o ON u.id = o.user_id;

-- LEFT JOIN — all rows from left, matching from right (NULLs if no match)
SELECT u.id, u.email, o.id AS order_id
FROM users u
LEFT JOIN orders o ON u.id = o.user_id;

-- Find users with NO orders (common test check)
SELECT u.id, u.email
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
WHERE o.id IS NULL;

-- Multiple JOINs
SELECT u.email, o.id AS order_id, p.name AS product_name
FROM users u
INNER JOIN orders o ON u.id = o.user_id
INNER JOIN order_items oi ON o.id = oi.order_id
INNER JOIN products p ON oi.product_id = p.id;
```

---

## Subqueries

```sql
-- Subquery in WHERE
SELECT * FROM orders
WHERE user_id IN (
  SELECT id FROM users WHERE status = 'inactive'
);

-- Subquery in FROM (derived table)
SELECT user_id, AVG(order_count) AS avg_orders
FROM (
  SELECT user_id, COUNT(*) AS order_count
  FROM orders
  GROUP BY user_id
) AS order_counts
GROUP BY user_id;

-- EXISTS (often faster than IN for large sets)
SELECT * FROM users u
WHERE EXISTS (
  SELECT 1 FROM orders o WHERE o.user_id = u.id
);
```

---

## Data Validation Queries

These are especially useful for QA to verify data integrity after test runs.

```sql
-- Check for NULL values in required columns
SELECT * FROM users WHERE email IS NULL OR name IS NULL;

-- Check for duplicate emails (should be unique)
SELECT email, COUNT(*) AS count
FROM users
GROUP BY email
HAVING COUNT(*) > 1;

-- Check for orphaned records (referential integrity)
SELECT * FROM orders
WHERE user_id NOT IN (SELECT id FROM users);

-- Check for records created in the last 24 hours
SELECT * FROM orders
WHERE created_at >= NOW() - INTERVAL '24 hours';

-- Verify total count after a bulk insert
SELECT COUNT(*) FROM products WHERE created_at >= '2024-01-01';

-- Find records updated but not as expected
SELECT * FROM users
WHERE updated_at = created_at AND role = 'admin';

-- Spot check: verify specific user data
SELECT id, email, status, created_at FROM users WHERE id = 42;
```

---

## Date & Time

```sql
-- Current timestamp
SELECT NOW();

-- Records from today
SELECT * FROM orders WHERE DATE(created_at) = CURRENT_DATE;

-- Records from the past 7 days
SELECT * FROM orders
WHERE created_at >= NOW() - INTERVAL '7 days';

-- Extract parts of a date
SELECT
  EXTRACT(YEAR FROM created_at) AS year,
  EXTRACT(MONTH FROM created_at) AS month,
  EXTRACT(DAY FROM created_at) AS day
FROM orders;

-- Format a date
SELECT TO_CHAR(created_at, 'YYYY-MM-DD') FROM orders;
```

---

## String Functions

```sql
-- Length of a string
SELECT id, email, LENGTH(email) AS email_length FROM users;

-- Convert case
SELECT UPPER(name), LOWER(email) FROM users;

-- Trim whitespace (useful for spotting data quality issues)
SELECT * FROM users WHERE name != TRIM(name);

-- Concatenate
SELECT CONCAT(first_name, ' ', last_name) AS full_name FROM users;
-- or use the || operator
SELECT first_name || ' ' || last_name AS full_name FROM users;

-- Substring
SELECT SUBSTRING(email FROM 1 FOR 5) FROM users;  -- first 5 chars

-- Replace
SELECT REPLACE(phone, '-', '') AS phone_clean FROM users;

-- Check if string contains a value
SELECT * FROM products WHERE LOWER(name) LIKE '%refund%';
```

---

## NULL Handling

```sql
-- Filter NULLs
SELECT * FROM users WHERE phone IS NULL;
SELECT * FROM users WHERE phone IS NOT NULL;

-- Replace NULL with a default (COALESCE returns first non-NULL)
SELECT id, COALESCE(phone, 'N/A') AS phone FROM users;

-- NULLIF — returns NULL if both values are equal (avoids division by zero)
SELECT revenue / NULLIF(orders_count, 0) AS avg_order_value FROM summary;

-- NULLs in aggregations (COUNT ignores NULLs)
SELECT COUNT(*) AS total_rows, COUNT(phone) AS rows_with_phone FROM users;
```

---

## Useful Test Scenarios

```sql
-- Verify a new user was inserted correctly
SELECT * FROM users ORDER BY created_at DESC LIMIT 5;

-- Check the state after a cancellation flow
SELECT id, status, cancelled_at, refund_amount
FROM orders WHERE id = 1001;

-- Confirm cascade delete worked
SELECT * FROM order_items WHERE order_id = 1001;

-- Count records per environment/tenant (for multi-tenant apps)
SELECT tenant_id, COUNT(*) FROM users GROUP BY tenant_id ORDER BY 2 DESC;

-- Find records stuck in a transitional state (e.g. pending > 1 hour)
SELECT * FROM orders
WHERE status = 'pending'
AND created_at < NOW() - INTERVAL '1 hour';

-- Compare row counts between two tables (data migration check)
SELECT 'users' AS tbl, COUNT(*) FROM users
UNION ALL
SELECT 'users_archive', COUNT(*) FROM users_archive;

-- Verify no test data leaked into production
SELECT * FROM users WHERE email LIKE '%test%' OR email LIKE '%example.com%';
```

---

## Tips for Testers

- Always use `LIMIT` when exploring unknown tables to avoid fetching millions of rows.
- Use `BEGIN` / `ROLLBACK` to safely run `INSERT`, `UPDATE`, or `DELETE` during testing without committing changes.
- Prefer `IS NULL` / `IS NOT NULL` over `= NULL` — the latter never matches anything.
- `COUNT(*)` counts all rows; `COUNT(column)` skips NULLs — know the difference.


```sql
BEGIN;
  DELETE FROM orders WHERE status = 'test';
  -- Check what would be deleted before committing
  SELECT COUNT(*) FROM orders WHERE status = 'test';
ROLLBACK;  -- or COMMIT; when you're sure
```

---

*notes: all examples use PostgreSQL syntax.*