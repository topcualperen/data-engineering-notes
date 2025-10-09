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
