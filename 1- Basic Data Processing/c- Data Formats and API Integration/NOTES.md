# Data Formats and API Integration

## Table of Contents
- Modern Data Formats
  - JSON vs CSV vs Parquet
  - Parquet Advantages
  - Schema Evolution
- API Data Extraction
  - REST API Fundamentals
  - Python Requests Library
  - Writing Api Data to Database

---

## Modern Data Contents

### What is a Data Format?

A data format is a standard that defines how data is structured and stored. As a data engineer, knowing which format to use when is critical because this choice directly impacts performance, cost and data process speed.

--- 

## JSON vs CSV vs Parquet

### CSV (Comma-Separated Values)

The simplest and most common data format. It is human-readable and text-based.

#### CSV Structure

```csv
user_id,name,email,age,city
1,Ali,ali@example.com,25,Istanbul
2,Ayşe,ayse@example.com,30,Ankara
3,Mehmet,mehmet@example.com,35,Izmir
```

#### CSV Advantages

- ✅ Human-readable
- ✅ Can be opened in any tool (Excel, text editor)
- ✅ Very common, universal support
- ✅ Basic structure, easy to understand
- ✅ Suitable for streaming (line-by-line reading)

#### CSV Disadvantages

- ❌ No data type information
- ❌ No support for nested structure
- ❌ Slow for large files
- ❌ No compression, takes up a lot of space
- ❌ No metadata (column types, descriptions, etc.)
- ❌ Special chars can cause issues

#### CSV Use Cases

- Small datasets (< 1GB)
- Data export/import (working with excel)
- Data that needs human inspection
- Simple, flat tables

---