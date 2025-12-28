# Testing and Quality Expertise

## Scope
This prompt provides testing patterns, quality assurance practices, and validation strategies for TheAlgorithms/Python.

## Testing Strategy

### 1. Doctest-First Approach

Doctests are the primary testing mechanism in this repository.

```python
def algorithm(data: list[int]) -> list[int]:
    """
    Algorithm description.
    
    Args:
        data: Input data
    
    Returns:
        Processed data
    
    # Happy Path Tests
    >>> algorithm([3, 1, 2])
    [1, 2, 3]
    >>> algorithm([1])
    [1]
    
    # Edge Cases
    >>> algorithm([])
    []
    >>> algorithm([1, 1, 1])
    [1, 1, 1]
    
    # Negative Numbers
    >>> algorithm([-3, -1, -2])
    [-3, -2, -1]
    
    # Mixed
    >>> algorithm([-1, 0, 1])
    [-1, 0, 1]
    
    # Comparison with stdlib
    >>> algorithm([5, 2, 8, 1]) == sorted([5, 2, 8, 1])
    True
    
    # Error Cases
    >>> algorithm(None)
    Traceback (most recent call last):
        ...
    TypeError: data must be a list
    
    >>> algorithm("not a list")
    Traceback (most recent call last):
        ...
    TypeError: data must be a list
    """
    if not isinstance(data, list):
        raise TypeError("data must be a list")
    # Implementation
    return sorted(data)
```

### 2. Test Categories

#### Essential Test Cases
```python
# 1. Happy Path - Normal operation
>>> func([1, 2, 3])
expected_result

# 2. Empty Input
>>> func([])
[]

# 3. Single Element
>>> func([1])
[1]

# 4. Two Elements
>>> func([2, 1])
[1, 2]

# 5. Already Sorted
>>> func([1, 2, 3])
[1, 2, 3]

# 6. Reverse Sorted
>>> func([3, 2, 1])
[1, 2, 3]

# 7. Duplicates
>>> func([1, 2, 2, 1])
[1, 1, 2, 2]

# 8. All Same
>>> func([1, 1, 1])
[1, 1, 1]

# 9. Negative Numbers
>>> func([-1, -2, -3])
[-3, -2, -1]

# 10. Mixed Positive/Negative
>>> func([-1, 0, 1])
[-1, 0, 1]
```

### 3. pytest Patterns

When doctests aren't sufficient, use pytest:

```python
# tests/test_algorithm.py
import pytest
from module.algorithm import func


class TestAlgorithm:
    """Test suite for algorithm."""

    # Parametrized tests for multiple inputs
    @pytest.mark.parametrize("input_data,expected", [
        ([3, 1, 2], [1, 2, 3]),
        ([], []),
        ([1], [1]),
        ([2, 1], [1, 2]),
    ])
    def test_sorting(self, input_data: list[int], expected: list[int]) -> None:
        assert func(input_data) == expected

    # Fixture for reusable setup
    @pytest.fixture
    def large_input(self) -> list[int]:
        return list(range(1000, 0, -1))

    def test_large_input(self, large_input: list[int]) -> None:
        result = func(large_input)
        assert result == sorted(large_input)

    # Exception testing
    def test_none_input_raises(self) -> None:
        with pytest.raises(TypeError, match="must be a list"):
            func(None)

    # Property-based testing
    def test_output_is_sorted(self) -> None:
        import random
        data = random.sample(range(100), 50)
        result = func(data)
        assert result == sorted(data)

    def test_preserves_length(self) -> None:
        import random
        data = random.sample(range(100), 50)
        result = func(data)
        assert len(result) == len(data)
```

## Quality Metrics

### 1. Code Coverage

```bash
# Run with coverage
pytest --cov=module --cov-report=html

# Target: >90% coverage
```

### 2. Ruff Checks

```bash
# All files
ruff check .

# Specific file
ruff check path/to/file.py

# Auto-fix
ruff check --fix .
```

### 3. Type Checking

```bash
# Run mypy
mypy --ignore-missing-imports path/to/file.py
```

### 4. Complexity Metrics

| Metric | Max Value | Tool |
|--------|-----------|------|
| McCabe Complexity | 17 | ruff (C901) |
| Function Arguments | 10 | ruff (PLR0913) |
| Function Statements | 50 | ruff (PLR0915) |
| Function Returns | 8 | ruff |
| Branches | 20 | ruff |

## Pre-Commit Checklist

```markdown
## Before Committing

### Automated Checks
- [ ] `ruff check .` - No errors
- [ ] `ruff format --check .` - Properly formatted
- [ ] `python -m doctest -v file.py` - All doctests pass
- [ ] `pytest` - All tests pass
- [ ] `pre-commit run --all-files` - All hooks pass

### Manual Checks
- [ ] Type hints on all public functions
- [ ] Docstrings with examples
- [ ] Reference URL in module docstring
- [ ] No `print()` statements (use return)
- [ ] No `input()` statements
- [ ] Descriptive variable names
```

## Performance Testing

### Time Benchmarking

```python
import timeit

def benchmark_algorithm(func, data_generator, sizes, runs=100):
    """
    Benchmark algorithm across input sizes.
    
    >>> def my_sort(x): return sorted(x)
    >>> def gen(n): return list(range(n, 0, -1))
    >>> results = benchmark_algorithm(my_sort, gen, [100, 1000])
    >>> all(t > 0 for t in results.values())
    True
    """
    results = {}
    for size in sizes:
        data = data_generator(size)
        timer = timeit.Timer(lambda: func(data.copy()))
        avg_time = timer.timeit(runs) / runs
        results[size] = avg_time
        print(f"Size {size}: {avg_time:.6f}s")
    return results
```

### Memory Profiling

```python
# Install: pip install memory_profiler

from memory_profiler import profile

@profile
def memory_test():
    data = list(range(100000))
    result = algorithm(data)
    return result
```

## Error Handling Patterns

### Input Validation

```python
def validate_input(data: list[int]) -> None:
    """
    Validate input data.
    
    >>> validate_input([1, 2, 3])  # No error
    >>> validate_input(None)
    Traceback (most recent call last):
        ...
    TypeError: data must be a list
    >>> validate_input([1, 'a', 2])
    Traceback (most recent call last):
        ...
    TypeError: all elements must be integers
    """
    if not isinstance(data, list):
        raise TypeError("data must be a list")
    if not all(isinstance(x, int) for x in data):
        raise TypeError("all elements must be integers")
```

### Exception Hierarchy

```python
class AlgorithmError(Exception):
    """Base exception for algorithm errors."""
    pass

class InvalidInputError(AlgorithmError):
    """Raised for invalid input."""
    pass

class EmptyStructureError(AlgorithmError):
    """Raised when operating on empty structure."""
    pass
```

## Test Report Template

```markdown
# Test Report: [Component]

## Summary
| Metric | Value |
|--------|-------|
| Total Tests | 50 |
| Passed | 48 |
| Failed | 2 |
| Coverage | 95% |

## Doctest Results
```
path/to/file.py
  - function_a: 5 tests, 5 passed
  - function_b: 3 tests, 3 passed
  - function_c: 4 tests, 2 passed, 2 FAILED
```

## Failed Tests
| Test | Expected | Actual | Fix |
|------|----------|--------|-----|
| test_edge_case | [] | Error | Add null check |

## Performance
| Operation | Target | Actual | Status |
|-----------|--------|--------|--------|
| Insert | O(log n) | O(log n) | ✅ |
| Search | O(log n) | O(log n) | ✅ |

## Recommendations
1. Add edge case handling for empty input
2. Improve test coverage for error paths
```
