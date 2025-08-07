# What is NumPy?
NumPy (Numerical Python) is the fundamental library for scientific computing in Python. It provides:
- High-performance multidimensional array objects (ndarray)
- Mathematical functions to operate on these arrays
- Tools for integrating with C/C++ and Fortran code
- Linear algebra, Fourier transform, and random number capabilities

Why NumPy is Essential for Data Science
1. Performance
Faster & more efficient than Python lists.
```
import numpy as np
import time

# Python list operations
python_list = list(range(1000000))
start = time.time()
result_list = [x * 2 for x in python_list]
python_time = time.time() - start

# NumPy array operations
numpy_array = np.arange(1000000)
start = time.time()
result_array = numpy_array * 2
numpy_time = time.time() - start

print(f"Python list time: {python_time:.4f} seconds")
print(f"NumPy array time: {numpy_time:.4f} seconds")
print(f"NumPy is {python_time/numpy_time:.1f}x faster")
```
NumPy operations are typically 10-100x faster than pure Python due to:
- Vectorization: Operations are applied to entire arrays at once
- C implementation: Core operations are implemented in C
- Memory efficiency: Contiguous memory layout and fixed data types

2. Memory Efficiency
```
pythonimport sys

# Memory usage comparison
python_list = [1, 2, 3, 4, 5] * 1000
numpy_array = np.array([1, 2, 3, 4, 5] * 1000)

print(f"Python list memory: {sys.getsizeof(python_list)} bytes")
print(f"NumPy array memory: {numpy_array.nbytes} bytes")
```
3. Foundation for the Ecosystem

Pandas: Built on NumPy arrays
Matplotlib: Uses NumPy for data handling
Scikit-learn: Expects NumPy arrays as input
TensorFlow/PyTorch: Interoperable with NumPy


## The ndarray Object: Understanding the core data structure of NumPy
### What is an ndarray?
The ndarray (N-dimensional array) is NumPy's main data structure. It's a collection of elements of the same type, arranged in a multidimensional grid.
### Key Attributes of ndarray
```
import numpy as np

# Create a sample array
arr = np.array([[1, 2, 3, 4],
                [5, 6, 7, 8],
                [9, 10, 11, 12]])

print("Array:")
print(arr)
print(f"Shape: {arr.shape}")           # (3, 4) - 3 rows, 4 columns
print(f"Dimensions: {arr.ndim}")       # 2 - two-dimensional
print(f"Size: {arr.size}")             # 12 - total number of elements
print(f"Data type: {arr.dtype}")       # int64 (or int32 on some systems)
print(f"Item size: {arr.itemsize}")    # 8 bytes per element
print(f"Total bytes: {arr.nbytes}")    # 96 bytes total
```
### Understanding Array Dimensions
```
# 1D array (vector)
arr_1d = np.array([1, 2, 3, 4])
print(f"1D array shape: {arr_1d.shape}")  # (4,)

# 2D array (matrix)
arr_2d = np.array([[1, 2, 3],
                   [4, 5, 6]])
print(f"2D array shape: {arr_2d.shape}")  # (2, 3)

# 3D array (tensor)
arr_3d = np.array([[[1, 2], [3, 4]],
                   [[5, 6], [7, 8]]])
print(f"3D array shape: {arr_3d.shape}")  # (2, 2, 2)
```

## Creating NumPy Arrays

- From Python Lists and Tuples
```
# From lists
list_1d = [1, 2, 3, 4, 5]
arr_from_list = np.array(list_1d)

# From nested lists (2D)
list_2d = [[1, 2, 3], [4, 5, 6]]
arr_from_nested = np.array(list_2d)

# From tuples
tuple_data = (1, 2, 3, 4)
arr_from_tuple = np.array(tuple_data)

print("From list:", arr_from_list)
print("From nested list:\n", arr_from_nested)
print("From tuple:", arr_from_tuple)
```
- Specifying Data Types
```
# Integer arrays
int_arr = np.array([1, 2, 3], dtype=np.int32)
print(f"Integer array: {int_arr}, dtype: {int_arr.dtype}")

# Float arrays
float_arr = np.array([1, 2, 3], dtype=np.float64)
print(f"Float array: {float_arr}, dtype: {float_arr.dtype}")

# Complex arrays
complex_arr = np.array([1+2j, 3+4j], dtype=np.complex128)
print(f"Complex array: {complex_arr}, dtype: {complex_arr.dtype}")

# Boolean arrays
bool_arr = np.array([True, False, True], dtype=np.bool_)
print(f"Boolean array: {bool_arr}, dtype: {bool_arr.dtype}")
```
- Built-in Array Creation Functions
1. Arrays of Zeros and Ones
```
# Arrays of zeros
zeros_1d = np.zeros(5)
zeros_2d = np.zeros((3, 4))
zeros_3d = np.zeros((2, 3, 4))

print("1D zeros:", zeros_1d)
print("2D zeros:\n", zeros_2d)

# Arrays of ones
ones_2d = np.ones((2, 3))
ones_int = np.ones((2, 3), dtype=np.int32)

print("2D ones:\n", ones_2d)
print("2D ones (int):\n", ones_int)
```
2. Arrays with Specific Values
```
# Array filled with a specific value
filled_arr = np.full((3, 3), 7)
print("Filled with 7:\n", filled_arr)

# Empty array (uninitialized)
empty_arr = np.empty((2, 2))  # Values are arbitrary
print("Empty array:\n", empty_arr)
```
3. Identity and Eye Matrices
```
# Identity matrix
identity = np.eye(3)
print("Identity matrix:\n", identity)

# Identity matrix with different dimensions
identity_rect = np.eye(3, 4)
print("3x4 identity:\n", identity_rect)

# Diagonal offset
eye_offset = np.eye(4, k=1)  # Diagonal shifted up by 1
print("Eye with offset:\n", eye_offset)
```
4. Range-based Array Creation
- Using arange()
```
# Basic range
range_arr = np.arange(10)
print("Range 0-9:", range_arr)

# With start and stop
range_start_stop = np.arange(2, 10)
print("Range 2-9:", range_start_stop)

# With step
range_step = np.arange(0, 20, 3)
print("Range with step:", range_step)

# Float ranges
float_range = np.arange(0, 2, 0.3)
print("Float range:", float_range)
```
Using linspace() and logspace()
```
python# Linear spacing
linear = np.linspace(0, 10, 5)  # 5 points from 0 to 10
print("Linear spacing:", linear)

# Include endpoint or not
linear_no_end = np.linspace(0, 10, 5, endpoint=False)
print("Linear (no endpoint):", linear_no_end)

# Logarithmic spacing
log_space = np.logspace(0, 2, 5)  # 5 points from 10^0 to 10^2
print("Logarithmic spacing:", log_space)

# Custom base for logspace
log_base2 = np.logspace(0, 3, 4, base=2)  # Base 2: 2^0 to 2^3
print("Log base 2:", log_base2)
```
Random Array Generation
```
python# Set seed for reproducibility
np.random.seed(42)

# Random floats between 0 and 1
random_uniform = np.random.random((2, 3))
print("Random uniform:\n", random_uniform)

# Random integers
random_ints = np.random.randint(0, 10, (2, 3))
print("Random integers:\n", random_ints)

# Normal distribution
random_normal = np.random.normal(0, 1, (2, 3))  # mean=0, std=1
print("Random normal:\n", random_normal)

# Random choice from array
choices = np.random.choice([1, 5, 9, 13], (2, 2))
print("Random choice:\n", choices)
```
Creating Arrays from Existing Arrays
```
pythonoriginal = np.array([1, 2, 3, 4])

# zeros_like, ones_like, full_like
zeros_like_orig = np.zeros_like(original)
ones_like_orig = np.ones_like(original)
full_like_orig = np.full_like(original, 99)

print("Original:", original)
print("Zeros like:", zeros_like_orig)
print("Ones like:", ones_like_orig)
print("Full like:", full_like_orig)

# Copy arrays
copy_arr = np.copy(original)
copy_arr[0] = 999
print("Original after copy modification:", original)
print("Modified copy:", copy_arr)
Meshgrid for Multi-dimensional Coordinates
python# Create coordinate matrices
x = np.linspace(-2, 2, 5)
y = np.linspace(-1, 1, 3)

X, Y = np.meshgrid(x, y)
print("X coordinates:\n", X)
print("Y coordinates:\n", Y)

# Calculate function over grid
Z = X**2 + Y**2
print("Z = X² + Y²:\n", Z)
```

## Array Creation from Python Lists

Converting 1D and 2D Python lists to NumPy arrays
Specifying data types during conversion
Understanding array properties (shape, dtype)

### Built-in Functions

arange(): Creates sequences with specified start, stop, and step
zeros(): Creates arrays filled with zeros in any shape
ones(): Creates arrays filled with ones in any shape
Bonus functions like eye(), full(), and linspace()

## Indexing and Slicing

Single element access and negative indexing
Slicing with start:stop:step notation
2D array indexing with row/column access
Boolean indexing for conditional selection

## Mathematical Operations

Element-wise arithmetic operations (+, -, *, /, **)
Scalar operations applied to entire arrays
Matrix multiplication using @ or np.dot()
Mathematical functions (sqrt, exp, log, trig functions)

## Broadcasting
Broadcasting is NumPy's way of performing operations on arrays with different shapes without explicitly reshaping them.
```
# Scalar with array (we've already seen this)
arr = np.array([1, 2, 3, 4])
result = arr * 10  # Scalar 10 is "broadcast" to match array shape

# Array with different shaped array
arr_2d = np.array([[1, 2, 3],
                   [4, 5, 6]])
arr_1d = np.array([10, 20, 30])

result = arr_2d + arr_1d  # 1D array is broadcast to each row
print("Broadcasting result:\n", result)
```
### Broadcasting Rules (Simplified):

- Arrays are aligned from the rightmost dimension
- Dimensions of size 1 can be "stretched" to match
- Missing dimensions are assumed to be size 1
- Demonstrations with 1D, 2D, and higher-dimensional arrays

```
import numpy as np

# ================================
# 1. CREATING ARRAYS FROM PYTHON LISTS
# ================================

print("1. Creating Arrays from Python Lists")
print("=" * 40)

# 1D array from list
list_1d = [1, 2, 3, 4, 5]
arr_1d = np.array(list_1d)
print(f"1D list: {list_1d}")
print(f"1D array: {arr_1d}")
print(f"Shape: {arr_1d.shape}, Dtype: {arr_1d.dtype}\n")

# 2D array from nested list
list_2d = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
arr_2d = np.array(list_2d)
print(f"2D list: {list_2d}")
print(f"2D array:\n{arr_2d}")
print(f"Shape: {arr_2d.shape}, Dtype: {arr_2d.dtype}\n")

# Specifying data type
float_arr = np.array([1, 2, 3, 4], dtype=float)
print(f"Float array: {float_arr}")
print(f"Dtype: {float_arr.dtype}\n")

# ================================
# 2. BUILT-IN FUNCTIONS
# ================================

print("2. Built-in Array Creation Functions")
print("=" * 40)

# arange - creates arrays with evenly spaced values
arr_arange1 = np.arange(10)  # 0 to 9
arr_arange2 = np.arange(2, 10)  # 2 to 9
arr_arange3 = np.arange(0, 10, 2)  # 0 to 9, step=2
arr_arange4 = np.arange(0, 5, 0.5)  # with float step

print(f"arange(10): {arr_arange1}")
print(f"arange(2, 10): {arr_arange2}")
print(f"arange(0, 10, 2): {arr_arange3}")
print(f"arange(0, 5, 0.5): {arr_arange4}\n")

# zeros - creates arrays filled with zeros
zeros_1d = np.zeros(5)
zeros_2d = np.zeros((3, 4))
zeros_int = np.zeros(5, dtype=int)

print(f"zeros(5): {zeros_1d}")
print(f"zeros((3, 4)):\n{zeros_2d}")
print(f"zeros(5, dtype=int): {zeros_int}\n")

# ones - creates arrays filled with ones
ones_1d = np.ones(4)
ones_2d = np.ones((2, 3))
ones_float = np.ones((2, 2), dtype=float)

print(f"ones(4): {ones_1d}")
print(f"ones((2, 3)):\n{ones_2d}")
print(f"ones((2, 2), dtype=float):\n{ones_float}\n")

# Other useful functions
identity_matrix = np.eye(3)  # Identity matrix
full_array = np.full((2, 3), 7)  # Array filled with specific value
linspace_arr = np.linspace(0, 1, 5)  # 5 evenly spaced points from 0 to 1

print(f"eye(3) - Identity matrix:\n{identity_matrix}")
print(f"full((2, 3), 7) - Array filled with 7:\n{full_array}")
print(f"linspace(0, 1, 5): {linspace_arr}\n")

# ================================
# 3. INDEXING AND SLICING
# ================================

print("3. Indexing and Slicing")
print("=" * 25)

# 1D array indexing
arr = np.array([10, 20, 30, 40, 50])
print(f"Array: {arr}")
print(f"arr[0]: {arr[0]}")  # First element
print(f"arr[-1]: {arr[-1]}")  # Last element
print(f"arr[1:4]: {arr[1:4]}")  # Slicing
print(f"arr[::2]: {arr[::2]}")  # Every second element
print(f"arr[::-1]: {arr[::-1]}")  # Reverse array
print()

# 2D array indexing
arr_2d = np.array([[1, 2, 3, 4], 
                   [5, 6, 7, 8], 
                   [9, 10, 11, 12]])
print(f"2D Array:\n{arr_2d}")
print(f"arr_2d[1, 2]: {arr_2d[1, 2]}")  # Row 1, Column 2
print(f"arr_2d[1, :]: {arr_2d[1, :]}")  # Entire row 1
print(f"arr_2d[:, 2]: {arr_2d[:, 2]}")  # Entire column 2
print(f"arr_2d[0:2, 1:3]:\n{arr_2d[0:2, 1:3]}")  # Subarray
print()

# Boolean indexing
arr = np.array([1, 5, 3, 8, 2, 7])
mask = arr > 4
print(f"Array: {arr}")
print(f"Mask (arr > 4): {mask}")
print(f"Elements > 4: {arr[mask]}")
print(f"Direct boolean indexing: {arr[arr > 4]}")
print()

# ================================
# 4. MATHEMATICAL OPERATIONS
# ================================

print("4. Mathematical Operations")
print("=" * 25)

# Element-wise operations
a = np.array([1, 2, 3, 4])
b = np.array([5, 6, 7, 8])

print(f"a = {a}")
print(f"b = {b}")
print(f"a + b = {a + b}")  # Addition
print(f"a - b = {a - b}")  # Subtraction
print(f"a * b = {a * b}")  # Element-wise multiplication
print(f"a / b = {a / b}")  # Division
print(f"a ** 2 = {a ** 2}")  # Power
print(f"a % 3 = {a % 3}")  # Modulo
print()

# Scalar operations
print("Scalar operations:")
print(f"a + 10 = {a + 10}")
print(f"a * 3 = {a * 3}")
print(f"a ** 0.5 = {a ** 0.5}")
print()

# Matrix operations
matrix_a = np.array([[1, 2], [3, 4]])
matrix_b = np.array([[5, 6], [7, 8]])

print(f"Matrix A:\n{matrix_a}")
print(f"Matrix B:\n{matrix_b}")
print(f"Element-wise multiplication A * B:\n{matrix_a * matrix_b}")
print(f"Matrix multiplication A @ B:\n{matrix_a @ matrix_b}")
print(f"Matrix multiplication np.dot(A, B):\n{np.dot(matrix_a, matrix_b)}")
print()

# Mathematical functions
arr = np.array([1, 4, 9, 16])
print(f"Array: {arr}")
print(f"Square root: {np.sqrt(arr)}")
print(f"Exponential: {np.exp([1, 2, 3])}")
print(f"Logarithm: {np.log([1, np.e, np.e**2])}")
print(f"Sin: {np.sin([0, np.pi/2, np.pi])}")
print()

# ================================
# 5. BROADCASTING
# ================================

print("5. Broadcasting")
print("=" * 15)

# Broadcasting with different shapes
arr_2d = np.array([[1, 2, 3],
                   [4, 5, 6]])
arr_1d = np.array([10, 20, 30])

print(f"2D array (2x3):\n{arr_2d}")
print(f"1D array (3,): {arr_1d}")
print(f"2D + 1D (broadcasting):\n{arr_2d + arr_1d}")
print()

# Broadcasting with scalar
print(f"2D array + scalar 100:\n{arr_2d + 100}")
print()

# Broadcasting with column vector
col_vector = np.array([[1], [2]])  # Shape (2, 1)
print(f"Column vector (2x1):\n{col_vector}")
print(f"2D array * column vector:\n{arr_2d * col_vector}")
print()

# More complex broadcasting
a = np.array([[[1, 2]]])  # Shape (1, 1, 2)
b = np.array([[3], [4]])  # Shape (2, 1)
result = a + b            # Result shape (2, 1, 2)

print(f"Array a shape {a.shape}: {a}")
print(f"Array b shape {b.shape}:\n{b}")
print(f"a + b result shape {result.shape}:\n{result}")
print()

# Broadcasting rules demonstration
print("Broadcasting Rules:")
print("1. Arrays are aligned from the rightmost dimension")
print("2. Dimensions of size 1 can be stretched")
print("3. Missing dimensions are assumed to be 1")

# Examples of valid broadcasting
examples = [
    ("(3,) + (3,)", "Same shape"),
    ("(3, 1) + (3,)", "Second array gets expanded to (3, 3)"),
    ("(3, 4) + (4,)", "Second array becomes (1, 4), then (3, 4)"),
    ("(3, 1, 4) + (2, 4)", "Second becomes (1, 2, 4), then (3, 2, 4)")
]

for shapes, explanation in examples:
    print(f"  {shapes}: {explanation}")

# ================================
# 6. PRACTICAL EXAMPLES
# ================================

print("\n6. Practical Examples")
print("=" * 20)

# Example 1: Temperature conversion
fahrenheit = np.array([32, 68, 86, 104])
celsius = (fahrenheit - 32) * 5/9
print(f"Fahrenheit: {fahrenheit}")
print(f"Celsius: {celsius.round(1)}")
print()

# Example 2: Normalizing data
data = np.array([1, 5, 10, 15, 20])
normalized = (data - np.mean(data)) / np.std(data)
print(f"Original data: {data}")
print(f"Normalized data: {normalized.round(3)}")
print()

# Example 3: Distance calculation
point1 = np.array([1, 2])
points = np.array([[3, 4], [5, 6], [7, 8]])
distances = np.sqrt(np.sum((points - point1)**2, axis=1))
print(f"Point 1: {point1}")
print(f"Other points:\n{points}")
print(f"Distances: {distances.round(3)}")
print()

print("Summary:")
print("- Arrays can be created from lists using np.array()")
print("- Built-in functions like arange, zeros, ones provide convenient creation methods")
print("- Indexing and slicing work similar to Python lists but with multi-dimensional support")
print("- Mathematical operations are element-wise by default")
print("- Broadcasting allows operations between arrays of different shapes")
```
