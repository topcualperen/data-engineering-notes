# Fundementals of Data Processing

## Contents
- #### Introduction to NumPy and Pandas

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
'''python
np.mean(numbers)    # Average
np.sum(numbers)     # Sum
np.std(numbers)     # Standard deviation

#### Importance from Perspective of Data Engineer
We will need to transform millions of rows of data in ETL (Extract, Transform, Load) processes.
NumPy's vectorization feature makes these operations much faster.

---

## The Pandas DataFrame Concept