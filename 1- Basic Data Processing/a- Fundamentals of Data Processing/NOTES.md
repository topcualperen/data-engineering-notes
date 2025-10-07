# Fundementals of Data Processing

## Contents
- #### Introduction to NumPy and Pandas
- #### NumPy Arrays vs Java Arrays
- #### The Pandas DataFrame Concept
- #### CSV/JSON Reading/Writing


## Introduction to NumPy and Pandas

Numpy and Pandas are the most commonly used libraries in the Python ecosystem for efficiently processing large datasets.

### NumPy (Numerical Python)
It is a optimized library for mathematical operations and numerical calculations.

### Pandas
It is a library built on top of NumPy, designed for data analysis and manipulation.
It is ideal for processing data in tables (as SQL tables)

---

## NumPy Arrays vs Java Arrays

### Java Arrays Example
```java
int[] numbers = {1,2,3,4,5,6,7};
for (int i = 0; i < numbers.length; i++) {
    numbers[i] = numbers[i] * 2;
}
```

### NumPy Arrays Example
```python
import numpy as np
numbers = np.array([1,2,3,4,5,6,7])
(vectorization)
numbers = numbers * 2
```

### Key Differences

#### 1. Vectorization
There is no need to write loops in NumPy. The operations are automatically applied to all elements.
This means both faster and less code.

#### 2. Size Flexibility
The array size is static in java.
You can easily modify it using operations such as reshape and resize in NumPy.

#### 3. Performance
NumPy is using optimizations were write in the C language.
It is much faster than Java, even for millions of lines of data.

#### 4. Mathematical Functions
Numpy comes with mathematical functions.
```python
np.mean(numbers)    # Average
np.sum(numbers)     # Sum
np.std(numbers)     # Standard deviation
```

#### Importance from Perspective of Data Engineer
We will need to transform millions of rows of data in ETL (Extract, Transform, Load) processes.
NumPy's vectorization feature makes these operations much faster.

---

## The Pandas DataFrame Concept
You can think of a DataFrame as a SQL table or an Excel sheet. It consists of rows and columns, but has much more powerful features.

### Basic Structure:
```python
import pandas as pd

# Create DataFrame
data = {
    'user_id': [1, 2, 3, 4],
    'name': ['Ali', 'Ayşe', 'Mehmet', 'Zeynep'],
    'age': [25, 30, 35, 28],
    'city': ['Istanbul', 'Ankara', 'Izmir', 'Istanbul']
}
df = pd.DataFrame(data)
```
**output:**
```
   user_id    name  age      city
0        1     Ali   25  Istanbul
1        2    Ayşe   30    Ankara
2        3  Mehmet   35     Izmir
3        4  Zeynep   28  Istanbul
```

### SQL Analogy for Backend Developers:

| SQL Operation | Pandas Equivalent |
|------------|------------------|
| `SELECT name, age FROM users` | `df[['name', 'age']]` |
| `WHERE age > 25` | `df[df['age'] > 25]` |
| `GROUP BY city` | `df.groupby('city')` |
| `ORDER BY age DESC` | `df.sort_values('age', ascending=False)` |

### Critical Qualities for a Data Engineer

#### 1. Missing Data Management
Data doesnt always come clean in real world
```python
df.isnull().sum()           # How many nulls are there iin each column?
df.dropna()                 # Drop rows that contain null values
df.fillna(0)                # Fill null values with 0
```

#### 2. Data Types
Each column has a data type. (int, float, string, datetime)
```python
df.dtypes                            # Show column data types
df['age'] = df['age'].astype(float)  # Data Type casting
```

#### 3. Aggregation
Summarizing big data
```python
df.groupby('city')['age'].mean()  # Average age by city
```

#### 4. Merge and Join
Like Join operations in SQL
```python
# Merging two DataFrames
orders = pd.DataFrame({...})
customers = pd.DataFrame({...})
merged = pd.merge(orders, customers, on='user_id')
```

---

## CSV/JSON Reading/Writing