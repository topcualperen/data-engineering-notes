# SQL Fundamentals

## Table of Contents
- **SQL Core Concepts**
- **Relational Model and Normalization**
- **Basic SQL Commands**
  - **SELECT**
  - **WHERE**
  - **JOIN**
  - **GROUP BY**
- **Window Functions**

---

## SQL Core Concepts

### What is SQL?

SQL (Structured Query Language) is the standard language for communicating with relational databases. As a data engineer, approximately 80% of your data will likely reside in SQL databases (PostgreSQL, MySQL, SQL Server, Oracle, etc.).

### SQL Core Components

1. **DDL (Data Definition Language)**: Defines database structure (CREATE, ALTER, DROP)
2. **DML (Data Manipulation Language)**: Data manipulation (SELECT, INSERT, UPDATE, DELETE)
3. **DCL (Data Control Language)**: Access control (GRANT, REVOKE)
4. **TCL (Transaction Control Language)**: Transaction control (COMMIT, ROLLBACK)

**Priority for Data Engineers:**
DML > DDL > TCL > DCL

---

## Relational Model and Normalization

### Relational Model

The relational model organizes data into tables (relations). Each table consists of rows (tuples) and columns (attributes).

**Core Concepts:**
- **Primary Key (PK)**: Column(s) that uniquely identify a row
- **Foreign Key (FK)**: Column(s) that reference a primary key in another table
- **Relationship**: Connections between tables (One-to-One, One-to-Many, Many-to-Many)

#### Example:

```sql
-- Users table
CREATE TABLE users (
    user_id INT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100) UNIQUE,
    created_at TIMESTAMP
);

-- Orders table
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    user_id INT,
    total_amount DECIMAL(10, 2),
    order_date DATE,
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);
```

### Normalization

Normalization is the process of organizing database design to reduce redundancy and ensure data integrity.

#### Normal Forms

**1NF (First Normal Form)**

Each cell must contain a single value (atomic values).

**❌ Bad Design:**
```
user_id | name   | phones
1       | Ali    | 555-1234, 555-5678
```

**✅ Good Design:**
```
user_id | name   | phone
1       | Ali    | 555-1234
1       | Ali    | 555-5678
```

**2NF (Second Normal Form)**

Must satisfy 1NF + Every column must be fully dependent on the primary key.

**❌ Bad Design:**
```
order_id | product_id | product_name | quantity
1        | 101        | Laptop       | 2
```
(product_name depends on product_id, not order_id)
**✅ Good Design:**
```
-- Orders table
order_id | product_id | quantity

-- Products table
product_id | product_name
```

**3NF (Third Normal Form)**

Must satisfy 2NF + No transitive dependencies (non-key columns shouldn't depend on other non-key columns).

**❌ Bad Design:**
```
order_id | customer_id | customer_city | customer_country
```
(customer_country, customer_city'ye bağlı / customer_country depends on customer_city)

**✅ Good Design:**
```
-- Orders
order_id | customer_id

-- Customers
customer_id | city_id

-- Cities
city_id | city_name | country
```

#### Normalization for Data Engineers

- **OLTP (Transactional) systems**: High normalization (3NF+) - Data consistency priority
- **OLAP (Analytics) systems**: Low normalization (Denormalized) - Query performance priority
- **Data Warehouse**: Uses Star/Snowflake schema (partially denormalized)

---

## Basic SQL Commands

### SELECT

Used to retrieve data from a database.

```sql
-- Basic selection
SELECT name, email FROM users;

-- All columns
SELECT * FROM users;

-- Distinct values
SELECT DISTINCT city FROM users;

-- Column alias
SELECT 
    name AS user_name,
    email AS contact_email
FROM users;

-- Calculations
SELECT 
    product_name,
    price,
    price * 1.18 AS price_with_tax
FROM products;

-- Limit (first N rows)
SELECT * FROM users LIMIT 10;

-- ORDER BY (sorting)
SELECT name, age 
FROM users 
ORDER BY age DESC;  -- DESC: Descending, ASC: Ascending
```

### WHERE.

Used to filter rows.

```sql
-- Basic comparison
SELECT * FROM users WHERE age > 25;

-- Equality
SELECT * FROM users WHERE city = 'Istanbul';

-- Logical operators
SELECT * FROM users 
WHERE age > 25 AND city = 'Istanbul';

SELECT * FROM users 
WHERE city = 'Istanbul' OR city = 'Ankara';

-- IN operator
SELECT * FROM users 
WHERE city IN ('Istanbul', 'Ankara', 'Izmir');

-- BETWEEN (range)
SELECT * FROM orders 
WHERE order_date BETWEEN '2024-01-01' AND '2024-12-31';

-- LIKE (pattern matching)
SELECT * FROM users 
WHERE email LIKE '%@gmail.com';  -- % = any characters
                                 -- _ = single character

-- NULL check
SELECT * FROM users WHERE phone IS NULL;
SELECT * FROM users WHERE phone IS NOT NULL;
```

### JOIN

Used to combine two or more tables. One of the most critical concepts for Data Engineers.

#### JOIN Types

**1. INNER JOIN**

Returns only matching records from both tables.

```sql
SELECT 
    u.name,
    u.email,
    o.order_id,
    o.total_amount
FROM users u
INNER JOIN orders o ON u.user_id = o.user_id;

-- Short form (implicit join) - Not recommended
SELECT u.name, o.order_id
FROM users u, orders o
WHERE u.user_id = o.user_id;
```

**2. LEFT JOIN (LEFT OUTER JOIN)**

Returns all records from the left table + matching records from the right table.

```sql
-- All users + their orders if any
SELECT 
    u.name,
    o.order_id,
    o.total_amount
FROM users u
LEFT JOIN orders o ON u.user_id = o.user_id;

-- Find users with no orders
SELECT u.name
FROM users u
LEFT JOIN orders o ON u.user_id = o.user_id
WHERE o.order_id IS NULL;
```

**3. RIGHT JOIN (RIGHT OUTER JOIN)**

Opposite of LEFT JOIN. Rarely used in practice.

```sql
SELECT 
    u.name,
    o.order_id
FROM users u
RIGHT JOIN orders o ON u.user_id = o.user_id;
```

**4. FULL OUTER JOIN**

Returns all records from both tables.

```sql
SELECT 
    u.name,
    o.order_id
FROM users u
FULL OUTER JOIN orders o ON u.user_id = o.user_id;
```

**5. CROSS JOIN**

Cartesian product (each row matches with every row from the other table).

```sql
-- Warning: Can produce very large result sets
SELECT 
    u.name,
    p.product_name
FROM users u
CROSS JOIN products p;
```

#### Multiple JOINs

```sql
-- Combine user, order, and product information
SELECT 
    u.name AS customer_name,
    o.order_id,
    o.order_date,
    p.product_name,
    oi.quantity,
    oi.quantity * p.price AS line_total
FROM users u
INNER JOIN orders o ON u.user_id = o.user_id
INNER JOIN order_items oi ON o.order_id = oi.order_id
INNER JOIN products p ON oi.product_id = p.product_id
WHERE o.order_date >= '2024-01-01';
```

#### JOIN Performance Tips

1. Use indexes on JOIN columns
2. Filter with WHERE first, then JOIN
3. SELECT only needed columns (avoid SELECT *)
4. Pay attention to JOIN order (small to large tables)

### GROUP BY

Used to group data and perform aggregate calculations for each group.

#### Aggregate Functions

```sql
-- COUNT: Count rows
SELECT COUNT(*) AS total_users FROM users;
SELECT COUNT(DISTINCT city) AS unique_cities FROM users;

-- SUM: Sum
SELECT SUM(total_amount) AS total_revenue FROM orders;

-- AVG: Average
SELECT AVG(age) AS average_age FROM users;

-- MAX, MIN: Maximum, Minimum
SELECT 
    MAX(total_amount) AS highest_order,
    MIN(total_amount) AS lowest_order
FROM orders;
```

#### Using GROUP BY

```sql
-- User count by city
SELECT 
    city,
    COUNT(*) AS user_count
FROM users
GROUP BY city;

-- Total order amount per user
SELECT 
    user_id,
    COUNT(*) AS order_count,
    SUM(total_amount) AS total_spent,
    AVG(total_amount) AS avg_order_value
FROM orders
GROUP BY user_id;

-- Revenue by year and month
SELECT 
    EXTRACT(YEAR FROM order_date) AS year,
    EXTRACT(MONTH FROM order_date) AS month,
    SUM(total_amount) AS monthly_revenue,
    COUNT(*) AS order_count
FROM orders
GROUP BY 
    EXTRACT(YEAR FROM order_date),
    EXTRACT(MONTH FROM order_date)
ORDER BY year, month;
```

#### HAVING Clause

Used to filter after GROUP BY (WHERE filters before grouping).

```sql
-- Users with more than 5 orders
SELECT 
    user_id,
    COUNT(*) AS order_count
FROM orders
GROUP BY user_id
HAVING COUNT(*) > 5;

-- Cities with avg order value > 100
SELECT 
    u.city,
    AVG(o.total_amount) AS avg_order_value,
    COUNT(o.order_id) AS order_count
FROM users u
INNER JOIN orders o ON u.user_id = o.user_id
GROUP BY u.city
HAVING AVG(o.total_amount) > 100
ORDER BY avg_order_value DESC;
```

#### GROUP BY with JOIN

```sql
-- Order summary per user
SELECT 
    u.name,
    u.email,
    COUNT(o.order_id) AS total_orders,
    COALESCE(SUM(o.total_amount), 0) AS total_spent,
    MAX(o.order_date) AS last_order_date
FROM users u
LEFT JOIN orders o ON u.user_id = o.user_id
GROUP BY u.user_id, u.name, u.email
ORDER BY total_spent DESC;
```

**Note:** `COALESCE` converts NULL values to 0

---

## Window Functions

Window functions perform calculations across row groups but don't collapse rows like GROUP BY. They produce a result for each row. A very powerful tool for data engineers.

### Basic Syntax

```sql
function_name() OVER (
    [PARTITION BY column]
    [ORDER BY column]
    [ROWS/RANGE clause]
)
```

### Ranking Functions

#### ROW_NUMBER()

Assigns a unique sequential number to each row.

```sql
-- Rank users by age
SELECT 
    name,
    age,
    ROW_NUMBER() OVER (ORDER BY age DESC) AS age_rank
FROM users;

-- Rank users within each city
SELECT 
    name,
    city,
    age,
    ROW_NUMBER() OVER (PARTITION BY city ORDER BY age DESC) AS rank_in_city
FROM users;
```

#### RANK() ve DENSE_RANK()

- `RANK()`: Assigns same rank to equal values, skips next rank
- `DENSE_RANK()`: Assigns same rank to equal values, doesn't skip next rank

```sql
SELECT 
    product_name,
    sales_amount,
    RANK() OVER (ORDER BY sales_amount DESC) AS rank,
    DENSE_RANK() OVER (ORDER BY sales_amount DESC) AS dense_rank
FROM product_sales;

-- Example output:
-- product_name | sales_amount | rank | dense_rank
-- Product A    | 1000        | 1    | 1
-- Product B    | 1000        | 1    | 1
-- Product C    | 800         | 3    | 2  (RANK skips, DENSE_RANK doesn't skip)
```

#### NTILE()

Divides rows into N equal groups (e.g., quartiles, deciles).

```sql
-- Divide users into 4 groups by age (quartiles)
SELECT 
    name,
    age,
    NTILE(4) OVER (ORDER BY age) AS age_quartile
FROM users;
```

### Aggregate Window Functions

Aggregate functions can be used as window functions.

```sql
-- Calculate total and individual percentage for each row
SELECT 
    product_name,
    sales_amount,
    SUM(sales_amount) OVER () AS total_sales,
    ROUND(sales_amount * 100.0 / SUM(sales_amount) OVER (), 2) AS percentage
FROM product_sales;

-- Cumulative sum
SELECT 
    order_date,
    total_amount,
    SUM(total_amount) OVER (ORDER BY order_date) AS running_total
FROM orders;

-- Moving average - Last 7 days
SELECT 
    order_date,
    total_amount,
    AVG(total_amount) OVER (
        ORDER BY order_date 
        ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
    ) AS moving_avg_7days
FROM daily_sales;
```

### LAG() ve LEAD()

- `LAG()`: Returns the value from the previous row
- `LEAD()`: Returns the value from the next row

```sql
-- Compare with previous order
SELECT 
    order_date,
    total_amount,
    LAG(total_amount, 1) OVER (ORDER BY order_date) AS previous_amount,
    total_amount - LAG(total_amount, 1) OVER (ORDER BY order_date) AS difference
FROM orders;

-- Days between orders per user
SELECT 
    user_id,
    order_date,
    LAG(order_date, 1) OVER (PARTITION BY user_id ORDER BY order_date) AS previous_order_date,
    order_date - LAG(order_date, 1) OVER (PARTITION BY user_id ORDER BY order_date) AS days_since_last_order
FROM orders;
```

### FIRST_VALUE() and LAST_VALUE()

```sql
-- Each user's first and last order
SELECT 
    user_id,
    order_date,
    total_amount,
    FIRST_VALUE(order_date) OVER (
        PARTITION BY user_id 
        ORDER BY order_date
    ) AS first_order_date,
    LAST_VALUE(order_date) OVER (
        PARTITION BY user_id 
        ORDER BY order_date
        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) AS last_order_date
FROM orders;
```

### Use Cases for Data Engineers

#### 1. Top N in each group

```sql
-- Top 3 selling products in each category
WITH ranked_products AS (
    SELECT 
        category,
        product_name,
        total_sales,
        ROW_NUMBER() OVER (PARTITION BY category ORDER BY total_sales DESC) AS rank
    FROM product_sales
)
SELECT *
FROM ranked_products
WHERE rank <= 3;
```

#### 2. Change analysis

```sql
-- Monthly growth rate
SELECT 
    month,
    revenue,
    LAG(revenue) OVER (ORDER BY month) AS previous_month,
    ROUND(
        (revenue - LAG(revenue) OVER (ORDER BY month)) * 100.0 / 
        LAG(revenue) OVER (ORDER BY month), 
        2
    ) AS growth_rate_percent
FROM monthly_revenue;
```

#### 3. Cumulative metrics

```sql
-- Cumulative spending per user
SELECT 
    user_id,
    order_date,
    total_amount,
    SUM(total_amount) OVER (
        PARTITION BY user_id 
        ORDER BY order_date
    ) AS cumulative_spending,
    COUNT(*) OVER (
        PARTITION BY user_id 
        ORDER BY order_date
    ) AS order_number
FROM orders;
```

---
