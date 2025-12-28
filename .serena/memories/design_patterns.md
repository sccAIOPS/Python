# Design Patterns and Best Practices

## Algorithm Design Principles

### 1. Single Responsibility
Each file should implement one algorithm or a closely related set of variations.

### 2. Pure Functions
Algorithms should be pure functions when possible:
- Same input → Same output
- No side effects
- No global state modification

### 3. Return, Don't Print
```python
# ❌ Bad
def fibonacci(n):
    result = compute_fib(n)
    print(result)

# ✅ Good  
def fibonacci(n: int) -> int:
    """Compute fibonacci number."""
    return compute_fib(n)
```

### 4. Iterative vs Recursive
Often provide both implementations for educational purposes:
```python
def bubble_sort_iterative(collection: list) -> list:
    """Iterative implementation."""
    pass

def bubble_sort_recursive(collection: list) -> list:
    """Recursive implementation."""
    pass
```

## Data Structure Patterns

### Use Dataclasses for Simple Structures
```python
from dataclasses import dataclass

@dataclass
class Node:
    data: Any
    next: Node | None = None
```

### Generic Types
Use `Any` or type parameters for flexible structures:
```python
from typing import Any

class Stack:
    def push(self, item: Any) -> None: ...
    def pop(self) -> Any: ...
```

## Error Handling Pattern

### Validate Input Early
```python
def factorial(n: int) -> int:
    """
    >>> factorial(-1)
    Traceback (most recent call last):
        ...
    ValueError: n must not be negative
    """
    if n < 0:
        raise ValueError("n must not be negative")
    # ... implementation
```

## Documentation Patterns

### Wikipedia/Source References
Always cite sources in docstrings:
```python
def algorithm():
    """
    Implementation of Algorithm X.
    
    Reference: https://en.wikipedia.org/wiki/Algorithm_X
    
    >>> algorithm(input)
    output
    """
```

### Comprehensive Doctests
```python
def gcd(a: int, b: int) -> int:
    """
    Calculate GCD using Euclidean algorithm.
    
    >>> gcd(12, 8)
    4
    >>> gcd(0, 5)  # Edge case
    5
    >>> gcd(5, 0)  # Edge case
    5
    >>> gcd(-12, 8)  # Handles negatives
    4
    >>> gcd(0, 0)
    Traceback (most recent call last):
        ...
    ValueError: Both numbers cannot be zero
    """
```

## Performance Benchmarking Pattern

Many algorithm files include benchmarks in `if __name__ == "__main__"`:
```python
if __name__ == "__main__":
    from timeit import Timer
    
    num_runs = 1000
    unsorted = [4, 2, 7, 1, 9, 3]
    
    timer = Timer("sort_func(unsorted[:])", globals=globals())
    print(f"Average time: {timer.timeit(num_runs) / num_runs:.6f}s")
```

## Testing Patterns

### Doctest as Primary Tests
- All public functions should have doctests
- Run with `python -m doctest -v file.py`

### Comparison with Standard Library
```python
>>> my_sort([3, 1, 2]) == sorted([3, 1, 2])
True
```

### Random Test Data
```python
>>> import random
>>> data = random.sample(range(-50, 50), 100)
>>> my_sort(data) == sorted(data)
True
```

## Import Organization
```python
# Standard library
from dataclasses import dataclass
from typing import Any

# Third-party (only if necessary)
import numpy as np

# Local imports (rare in this project)
from .helper import helper_function
```

## File Naming Patterns
- `algorithm_name.py` - Single algorithm
- `algorithm_name_variant.py` - Algorithm variant
- `test_algorithm.py` - Dedicated test file (rare, use doctests)

## Module Organization
Each algorithm category directory has:
- `__init__.py` - Usually empty, marks as package
- `README.md` - Optional, describes the category
- `algorithm1.py`, `algorithm2.py`, etc.

## Code Reuse
- Avoid duplicating algorithms across files
- Import common utilities when needed
- Keep implementations self-contained for educational clarity
