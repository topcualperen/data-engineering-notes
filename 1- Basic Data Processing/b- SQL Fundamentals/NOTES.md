# SQL Fundamentals

## Table of Contents
- [SQL Core Concepts](#sql-core-concepts)
- [Relational Model ve Normalization](#relational-model-ve-normalization)
- [Basic SQL Commands](#basic-sql-commands)
  - [SELECT](#select)
  - [WHERE](#where)
  - [JOIN](#join)
  - [GROUP BY](#group-by)
- [Window Functions](#window-functions)

---

## SQL Core Concepts

### What is SQL?

SQL (Structured Query Language) is the standard language for communicating with relational databases. As a data engineer, approximately 80% of your data will likely reside in SQL databases (PostgreSQL, MySQL, SQL Server, Oracle, etc.).

### SQL Core Components

**Türkçe:**
1. **DDL (Data Definition Language)**: Defines database structure (CREATE, ALTER, DROP)
2. **DML (Data Manipulation Language)**: Data manipulation (SELECT, INSERT, UPDATE, DELETE)
3. **DCL (Data Control Language)**: Access control (GRANT, REVOKE)
4. **TCL (Transaction Control Language)**: Transaction control (COMMIT, ROLLBACK)

**Priority for Data Engineers:**
DML > DDL > TCL > DCL

---
