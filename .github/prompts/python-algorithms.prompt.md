# Python Algorithm Implementation Expertise

## Scope
This prompt provides domain expertise for implementing algorithms in Python, following TheAlgorithms/Python conventions.

## Core Principles

### 1. Pure Functions
- Same input always produces same output
- No side effects (no global state modification)
- Return results, don't print them

```python
# ✅ Good - Pure function
def factorial(n: int) -> int:
    """Calculate factorial."""
    if n < 0:
        raise ValueError("n must be non-negative")
    if n <= 1:
        return 1
    return n * factorial(n - 1)

# ❌ Bad - Side effects
def factorial(n: int) -> None:
    result = 1
    for i in range(1, n + 1):
        result *= i
    print(result)  # Don't print!
```

### 2. Type Hints

```python
from typing import Any, TypeVar

T = TypeVar("T")

# Function with generics
def find_max(items: list[T]) -> T:
    """Find maximum in comparable items."""
    ...

# Union types (modern syntax)
def process(value: int | float | None) -> str:
    ...

# Collections (modern syntax)
def sort_items(items: list[int]) -> list[int]:
    ...
```

### 3. Docstrings with Doctests

```python
def gcd(a: int, b: int) -> int:
    """
    Calculate the Greatest Common Divisor using Euclidean algorithm.
    
    Reference: https://en.wikipedia.org/wiki/Euclidean_algorithm
    
    Args:
        a: First integer
        b: Second integer
    
    Returns:
        Greatest common divisor of a and b
    
    Examples:
        >>> gcd(48, 18)
        6
        >>> gcd(0, 5)
        5
        >>> gcd(5, 0)
        5
        >>> gcd(-12, 8)
        4
        >>> gcd(0, 0)
        Traceback (most recent call last):
            ...
        ValueError: Both numbers cannot be zero
    """
    if a == 0 and b == 0:
        raise ValueError("Both numbers cannot be zero")
    a, b = abs(a), abs(b)
    while b:
        a, b = b, a % b
    return a
```

### 4. Error Handling

```python
def binary_search(arr: list[int], target: int) -> int:
    """
    Binary search in sorted array.
    
    Args:
        arr: Sorted list of integers
        target: Value to find
    
    Returns:
        Index of target, or -1 if not found
    
    Raises:
        TypeError: If arr is not a list
        ValueError: If arr is not sorted
    
    >>> binary_search([1, 2, 3], 2)
    1
    >>> binary_search(None, 1)
    Traceback (most recent call last):
        ...
    TypeError: arr must be a list
    """
    if not isinstance(arr, list):
        raise TypeError("arr must be a list")
    ...
```

### 5. Algorithm File Structure

```python
"""
Algorithm Name implementation.

Brief description of what the algorithm does and its use cases.

Reference: https://en.wikipedia.org/wiki/Algorithm_Name

Time Complexity: O(n log n)
Space Complexity: O(n)
"""

from __future__ import annotations

# Standard library imports
from dataclasses import dataclass
from typing import Any, Iterator, TypeVar

# Type variables
T = TypeVar("T")

# Constants
DEFAULT_SIZE = 10


# Data classes
@dataclass
class Node:
    """A node in the structure."""
    data: Any
    next: Node | None = None


# Main implementation
class Algorithm:
    """Main algorithm class."""
    
    def __init__(self) -> None:
        """Initialize algorithm."""
        pass
    
    def execute(self, data: list[T]) -> list[T]:
        """
        Execute the algorithm.
        
        >>> algo = Algorithm()
        >>> algo.execute([3, 1, 2])
        [1, 2, 3]
        """
        pass


# Standalone functions
def helper_function(value: int) -> int:
    """Helper with doctests."""
    pass


# Main block
if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

## Algorithm Categories

### Sorting Algorithms
- Always maintain stability documentation
- Include in-place vs copy behavior
- Document comparison function support

### Search Algorithms
- Document sorted/unsorted requirements
- Include found/not-found return conventions
- Handle empty input gracefully

### Graph Algorithms
- Support adjacency list and matrix representations
- Document directed/undirected handling
- Include cycle detection where relevant

### Dynamic Programming
- Document state and transition functions
- Include memoization vs tabulation variants
- Provide space optimization notes

### Tree/Graph Traversals
- Support iterative and recursive variants
- Document pre/in/post order where applicable
- Handle empty structures gracefully

## Common Patterns

### Iterator Pattern
```python
class LinkedList:
    def __iter__(self) -> Iterator[Any]:
        current = self.head
        while current:
            yield current.data
            current = current.next
```

### Factory Pattern
```python
def create_tree(values: list[int]) -> BinaryTree:
    """Create balanced BST from sorted values."""
    tree = BinaryTree()
    for value in values:
        tree.insert(value)
    return tree
```

### Comparison Functions
```python
from typing import Callable

def sort_with_key(
    items: list[T],
    key: Callable[[T], Any] = lambda x: x
) -> list[T]:
    """Sort with custom key function."""
    ...
```
