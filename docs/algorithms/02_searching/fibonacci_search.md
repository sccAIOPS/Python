# Fibonacci Search

## Overview

| Property | Value |
|----------|-------|
| **Category** | Division-Free Search |
| **Complexity (Time)** | O(log n) |
| **Complexity (Space)** | O(1) |
| **Requires Sorted** | Yes |
| **Key Feature** | Uses only addition and subtraction |

## Description

Fibonacci Search is a comparison-based search algorithm that divides a sorted array using Fibonacci numbers instead of halving. The key advantage is that it uses only addition and subtraction for index calculations, avoiding the more expensive division operation required by binary search.

This algorithm is particularly useful on systems where division is costly, or when data must be accessed sequentially (tape drives, linked lists) since Fibonacci search can be modified to access elements in a more sequential pattern.

## Mathematical Foundation

### Fibonacci Sequence

The Fibonacci sequence is defined as:

$$F_0 = 0, \quad F_1 = 1$$
$$F_n = F_{n-1} + F_{n-2} \text{ for } n \geq 2$$

**Key Property (Zeckendorf's Theorem):** Any positive integer can be uniquely represented as a sum of non-consecutive Fibonacci numbers.

### Array Division

Given array of size $n$, find smallest $k$ such that $F_k \geq n$:
- Array split point: $F_{k-2}$ from left
- Left segment: $F_{k-2}$ elements
- Right segment: $F_{k-1} - 1$ elements

### Golden Ratio Connection

The ratio of consecutive Fibonacci numbers approaches the golden ratio:

$$\lim_{n \to \infty} \frac{F_n}{F_{n-1}} = \phi = \frac{1 + \sqrt{5}}{2} \approx 1.618$$

This means each split divides the array approximately in golden ratio proportions.

### Complexity Analysis

**Time Complexity:**
- Each step eliminates either $F_{k-2}$ or $F_{k-1}$ elements
- After eliminating, problem reduces by at least one Fibonacci level
- Number of Fibonacci numbers ≤ n: $O(\log_\phi n) = O(\log n)$

**Recurrence:**
$$T(F_k) = T(F_{k-1}) + O(1)$$

Solution: $T(n) = O(\log_\phi n) = O(\log n)$

## Algorithm

### Pseudocode

```
FIBONACCI-SEARCH(A, n, target):
    // Find smallest Fibonacci number >= n
    fib_k_minus_2 ← 0        // F(k-2)
    fib_k_minus_1 ← 1        // F(k-1)
    fib_k ← 1                // F(k)
    
    while fib_k < n:
        fib_k_minus_2 ← fib_k_minus_1
        fib_k_minus_1 ← fib_k
        fib_k ← fib_k_minus_1 + fib_k_minus_2
    
    offset ← -1    // Marks eliminated range
    
    while fib_k > 1:
        // Check if valid index
        i ← min(offset + fib_k_minus_2, n - 1)
        
        if A[i] < target:
            // Target in right part, shift down two levels
            fib_k ← fib_k_minus_1
            fib_k_minus_1 ← fib_k_minus_2
            fib_k_minus_2 ← fib_k - fib_k_minus_1
            offset ← i
        else if A[i] > target:
            // Target in left part, shift down one level
            fib_k ← fib_k_minus_2
            fib_k_minus_1 ← fib_k_minus_1 - fib_k_minus_2
            fib_k_minus_2 ← fib_k - fib_k_minus_1
        else:
            return i    // Found
    
    // Check last element
    if fib_k_minus_1 = 1 and A[offset + 1] = target:
        return offset + 1
    
    return -1    // Not found
```

### Step-by-Step Execution

```
Input: A = [10, 22, 35, 40, 45, 50, 80, 82, 85, 90, 100], target = 85
       n = 11

Step 1: Find Fibonacci >= 11
  F = 0, 1, 1, 2, 3, 5, 8, 13
  Use F(7) = 13 >= 11
  fib_k=13, fib_k_minus_1=8, fib_k_minus_2=5

Step 2: offset = -1
  i = min(-1 + 5, 10) = 4
  A[4] = 45 < 85
  Move to right part:
    fib_k=8, fib_k_minus_1=5, fib_k_minus_2=3
    offset=4

Step 3: 
  i = min(4 + 3, 10) = 7
  A[7] = 82 < 85
  Move to right part:
    fib_k=5, fib_k_minus_1=3, fib_k_minus_2=2
    offset=7

Step 4:
  i = min(7 + 2, 10) = 9
  A[9] = 90 > 85
  Move to left part:
    fib_k=2, fib_k_minus_1=1, fib_k_minus_2=1
    offset=7

Step 5:
  i = min(7 + 1, 10) = 8
  A[8] = 85 = target ✓

Return: 8
```

## Complexity Analysis

### Time Complexity

| Case | Complexity |
|------|------------|
| Best | O(1) |
| Average | O(log n) |
| Worst | O(log n) |

### Space Complexity

| Implementation | Space |
|----------------|-------|
| Iterative | O(1) |

### Operation Comparison

| Operation | Binary Search | Fibonacci Search |
|-----------|---------------|------------------|
| Division | 1 per iteration | 0 |
| Addition | 0-1 per iteration | 2-3 per iteration |
| Comparison | 1-2 per iteration | 1-2 per iteration |

On CPUs where division is expensive (older/embedded), Fibonacci search can be faster.

## Visual Representation

```mermaid
flowchart TD
    A[Start] --> B[Find F_k >= n]
    B --> C[offset = -1]
    C --> D{fib_k > 1?}
    D -->|No| E[Check last element]
    E --> F[Return -1 or index]
    D -->|Yes| G[i = min offset + F_k-2, n-1]
    G --> H{A[i] vs target}
    H -->|A[i] < target| I[Shift right: offset=i]
    H -->|A[i] > target| J[Shift left]
    H -->|A[i] = target| K[Return i]
    I --> L[fib_k=F_k-1, etc]
    J --> M[fib_k=F_k-2, etc]
    L --> D
    M --> D
```

### Fibonacci Division Visualization

```
Array: [10, 22, 35, 40, 45, 50, 80, 82, 85, 90, 100]
Size: 11, F(7)=13 >= 11

Fibonacci: 0, 1, 1, 2, 3, 5, 8, 13
                    ^F(k-2)
                       ^F(k-1)
                          ^F(k)

Initial split at index 5 (offset + F(k-2) = -1 + 5):
[10, 22, 35, 40, 45] | [50, 80, 82, 85, 90, 100]
         F(k-2)=5            F(k-1)-1=7
```

## Implementation

### Python Implementation

```python
def fibonacci_search(arr: list[int], target: int) -> int:
    """
    Searches for an item in a sorted collection using Fibonacci search.
    
    Uses Fibonacci numbers to divide the array, requiring only
    addition and subtraction operations (no division).
    
    Args:
        arr: A sorted list of integers
        target: The element to search for
    
    Returns:
        Index of target if found, -1 otherwise
    
    Examples:
        >>> fibonacci_search([4, 5, 6, 7], 4)
        0
        >>> fibonacci_search([4, 5, 6, 7], 7)
        3
        >>> fibonacci_search([4, 5, 6, 7], -10)
        -1
        >>> fibonacci_search([], 10)
        -1
    """
    if not arr:
        return -1
    
    n = len(arr)
    
    # Find smallest Fibonacci >= n
    fib_k_minus_2 = 0  # F(k-2)
    fib_k_minus_1 = 1  # F(k-1)
    fib_k = 1          # F(k)
    
    while fib_k < n:
        fib_k_minus_2 = fib_k_minus_1
        fib_k_minus_1 = fib_k
        fib_k = fib_k_minus_1 + fib_k_minus_2
    
    # Offset marks the eliminated range
    offset = -1
    
    while fib_k > 1:
        # Check valid index
        i = min(offset + fib_k_minus_2, n - 1)
        
        if arr[i] < target:
            # Target in right part
            fib_k = fib_k_minus_1
            fib_k_minus_1 = fib_k_minus_2
            fib_k_minus_2 = fib_k - fib_k_minus_1
            offset = i
        elif arr[i] > target:
            # Target in left part
            fib_k = fib_k_minus_2
            fib_k_minus_1 = fib_k_minus_1 - fib_k_minus_2
            fib_k_minus_2 = fib_k - fib_k_minus_1
        else:
            return i  # Found
    
    # Check last element
    if fib_k_minus_1 and offset + 1 < n and arr[offset + 1] == target:
        return offset + 1
    
    return -1
```

### Optimized Implementation with Precomputed Fibonacci

```python
from functools import lru_cache

@lru_cache(maxsize=None)
def fibonacci(n: int) -> int:
    """Compute nth Fibonacci number with memoization."""
    if n < 2:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)


def fibonacci_search_optimized(arr: list[int], target: int) -> int:
    """
    Fibonacci search with precomputed Fibonacci numbers.
    
    >>> fibonacci_search_optimized([1, 2, 3, 4, 5, 6, 7, 8, 9], 6)
    5
    >>> fibonacci_search_optimized([1, 2, 3, 4, 5], 10)
    -1
    """
    if not arr:
        return -1
    
    n = len(arr)
    
    # Find k where F(k) >= n
    k = 0
    while fibonacci(k) < n:
        k += 1
    
    offset = -1
    
    while k > 1:
        # Index to check
        fib_k_minus_2 = fibonacci(k - 2)
        i = min(offset + fib_k_minus_2, n - 1)
        
        if arr[i] < target:
            k -= 1
            offset = i
        elif arr[i] > target:
            k -= 2
        else:
            return i
    
    # Check remaining element
    if k == 1 and offset + 1 < n and arr[offset + 1] == target:
        return offset + 1
    
    return -1
```

### Generic Implementation

```python
from typing import TypeVar, Protocol

class Comparable(Protocol):
    def __lt__(self, other: "Comparable") -> bool: ...
    def __gt__(self, other: "Comparable") -> bool: ...
    def __eq__(self, other: object) -> bool: ...

T = TypeVar('T', bound=Comparable)


def fibonacci_search_generic(arr: list[T], target: T) -> int:
    """
    Generic Fibonacci search for any comparable type.
    
    >>> fibonacci_search_generic(['a', 'c', 'e', 'g'], 'e')
    2
    >>> fibonacci_search_generic([1.0, 2.0, 3.0], 2.0)
    1
    """
    if not arr:
        return -1
    
    n = len(arr)
    
    # Generate Fibonacci numbers
    fib_k_minus_2, fib_k_minus_1 = 0, 1
    fib_k = fib_k_minus_1 + fib_k_minus_2
    
    while fib_k < n:
        fib_k_minus_2 = fib_k_minus_1
        fib_k_minus_1 = fib_k
        fib_k = fib_k_minus_1 + fib_k_minus_2
    
    offset = -1
    
    while fib_k > 1:
        i = min(offset + fib_k_minus_2, n - 1)
        
        if arr[i] < target:
            fib_k = fib_k_minus_1
            fib_k_minus_1 = fib_k_minus_2
            fib_k_minus_2 = fib_k - fib_k_minus_1
            offset = i
        elif arr[i] > target:
            fib_k = fib_k_minus_2
            fib_k_minus_1 = fib_k_minus_1 - fib_k_minus_2
            fib_k_minus_2 = fib_k - fib_k_minus_1
        else:
            return i
    
    if fib_k_minus_1 and offset + 1 < n and arr[offset + 1] == target:
        return offset + 1
    
    return -1
```

## Real-World Applications

### 1. Embedded Systems Search

```python
class EmbeddedTableLookup:
    """
    Lookup table for embedded systems where division is expensive.
    Uses Fibonacci search to avoid division operations.
    """
    
    def __init__(self, table: list[tuple[int, int]]):
        """
        Initialize with sorted (key, value) pairs.
        
        Args:
            table: List of (key, value) tuples sorted by key
        """
        self.table = sorted(table, key=lambda x: x[0])
        self._precompute_fibonacci()
    
    def _precompute_fibonacci(self) -> None:
        """Precompute Fibonacci numbers up to table size."""
        n = len(self.table)
        self.fibs = [0, 1]
        while self.fibs[-1] < n:
            self.fibs.append(self.fibs[-1] + self.fibs[-2])
    
    def lookup(self, key: int) -> int | None:
        """
        Look up value by key using Fibonacci search.
        Only uses addition and subtraction.
        
        >>> table = EmbeddedTableLookup([(i, i**2) for i in range(100)])
        >>> table.lookup(50)
        2500
        >>> table.lookup(1000) is None
        True
        """
        if not self.table:
            return None
        
        n = len(self.table)
        
        # Find k where F(k) >= n
        k = 2
        while self.fibs[k] < n:
            k += 1
        
        # Use precomputed values
        fib_k = self.fibs[k]
        fib_k_minus_1 = self.fibs[k - 1]
        fib_k_minus_2 = self.fibs[k - 2]
        
        offset = -1
        
        while fib_k > 1:
            # Only addition used here
            i = offset + fib_k_minus_2
            if i >= n:
                i = n - 1
            
            current_key = self.table[i][0]
            
            if current_key < key:
                fib_k = fib_k_minus_1
                fib_k_minus_1 = fib_k_minus_2
                fib_k_minus_2 = fib_k - fib_k_minus_1  # subtraction
                offset = i
            elif current_key > key:
                fib_k = fib_k_minus_2
                temp = fib_k_minus_1 - fib_k_minus_2  # subtraction
                fib_k_minus_1 = fib_k_minus_2
                fib_k_minus_2 = temp
            else:
                return self.table[i][1]
        
        # Check last position
        check_idx = offset + 1
        if fib_k_minus_1 and check_idx < n:
            if self.table[check_idx][0] == key:
                return self.table[check_idx][1]
        
        return None
```

### 2. Sequential Access Optimization

```python
class TapeStorage:
    """
    Simulates tape drive where sequential access is preferred.
    Fibonacci search minimizes backward seeks.
    """
    
    def __init__(self, data: list[tuple[int, str]]):
        """Initialize with sorted (key, data) records."""
        self.records = sorted(data, key=lambda x: x[0])
        self.current_position = 0
        self.seek_count = 0
        self.read_count = 0
    
    def _seek_to(self, position: int) -> None:
        """Simulate seeking to position."""
        if position != self.current_position:
            self.seek_count += 1
            self.current_position = position
    
    def _read_at(self, position: int) -> tuple[int, str]:
        """Read record at position."""
        self._seek_to(position)
        self.read_count += 1
        return self.records[position]
    
    def fibonacci_search(self, key: int) -> str | None:
        """
        Search using Fibonacci, which tends to access nearby positions.
        
        >>> tape = TapeStorage([(i*10, f'data_{i}') for i in range(50)])
        >>> tape.fibonacci_search(250)
        'data_25'
        """
        if not self.records:
            return None
        
        n = len(self.records)
        
        # Generate Fibonacci numbers
        fib_k_minus_2, fib_k_minus_1 = 0, 1
        fib_k = 1
        while fib_k < n:
            fib_k_minus_2 = fib_k_minus_1
            fib_k_minus_1 = fib_k
            fib_k = fib_k_minus_1 + fib_k_minus_2
        
        offset = -1
        
        while fib_k > 1:
            i = min(offset + fib_k_minus_2, n - 1)
            
            record_key, record_data = self._read_at(i)
            
            if record_key < key:
                fib_k = fib_k_minus_1
                fib_k_minus_1 = fib_k_minus_2
                fib_k_minus_2 = fib_k - fib_k_minus_1
                offset = i
            elif record_key > key:
                fib_k = fib_k_minus_2
                fib_k_minus_1 = fib_k_minus_1 - fib_k_minus_2
                fib_k_minus_2 = fib_k - fib_k_minus_1
            else:
                return record_data
        
        # Check final position
        if fib_k_minus_1 and offset + 1 < n:
            record_key, record_data = self._read_at(offset + 1)
            if record_key == key:
                return record_data
        
        return None
    
    def get_stats(self) -> dict:
        """Return search statistics."""
        return {
            'seeks': self.seek_count,
            'reads': self.read_count
        }
```

### 3. Memory-Constrained Search

```python
class ConstantSpaceSearch:
    """
    Search optimized for systems with very limited RAM.
    Uses O(1) extra space by computing Fibonacci on the fly.
    """
    
    def __init__(self, sorted_data: list[int]):
        self.data = sorted_data
    
    def search(self, target: int) -> int:
        """
        Search with constant extra space.
        
        >>> searcher = ConstantSpaceSearch(list(range(0, 1000, 10)))
        >>> searcher.search(500)
        50
        """
        if not self.data:
            return -1
        
        n = len(self.data)
        
        # Compute Fibonacci numbers (only 3 values stored)
        f0, f1, fk = 0, 1, 1
        while fk < n:
            f0, f1 = f1, fk
            fk = f0 + f1
        
        offset = -1
        
        while fk > 1:
            i = offset + f0
            if i >= n:
                i = n - 1
            
            if self.data[i] < target:
                fk, f1, f0 = f1, f0, f1 - f0
                offset = i
            elif self.data[i] > target:
                fk, f1, f0 = f0, f1 - f0, fk - f1
            else:
                return i
        
        if f1 and offset + 1 < n and self.data[offset + 1] == target:
            return offset + 1
        
        return -1
```

### 4. Database Page Search

```python
class BTreePageSearch:
    """
    Simulates B-tree page search using Fibonacci.
    Useful when page reads are expensive.
    """
    
    def __init__(self, keys: list[int], page_size: int = 100):
        self.keys = sorted(keys)
        self.page_size = page_size
        self.pages_read = 0
    
    def _read_page(self, page_num: int) -> list[int]:
        """Simulate reading a page from disk."""
        self.pages_read += 1
        start = page_num * self.page_size
        end = min(start + self.page_size, len(self.keys))
        return self.keys[start:end]
    
    def search(self, key: int) -> int:
        """
        Search for key using Fibonacci-based page selection.
        
        >>> db = BTreePageSearch(list(range(10000)))
        >>> db.search(5000)
        5000
        """
        if not self.keys:
            return -1
        
        n = len(self.keys)
        num_pages = (n + self.page_size - 1) // self.page_size
        
        # Fibonacci search over pages
        f0, f1, fk = 0, 1, 1
        while fk < num_pages:
            f0, f1 = f1, fk
            fk = f0 + f1
        
        page_offset = -1
        
        while fk > 1:
            page_idx = page_offset + f0
            if page_idx >= num_pages:
                page_idx = num_pages - 1
            
            page = self._read_page(page_idx)
            
            if page[-1] < key:
                fk, f1, f0 = f1, f0, f1 - f0
                page_offset = page_idx
            elif page[0] > key:
                fk, f1, f0 = f0, f1 - f0, fk - f1
            else:
                # Key might be in this page, do linear search
                for i, k in enumerate(page):
                    if k == key:
                        return page_idx * self.page_size + i
                return -1
        
        # Check final page
        if f1 and page_offset + 1 < num_pages:
            page = self._read_page(page_offset + 1)
            for i, k in enumerate(page):
                if k == key:
                    return (page_offset + 1) * self.page_size + i
        
        return -1
```

### 5. Calibration Table Lookup

```python
class SensorCalibrationTable:
    """
    Calibration lookup for sensor readings.
    Uses Fibonacci search for microcontroller compatibility.
    """
    
    def __init__(self):
        # (raw_reading, calibrated_value) pairs
        self.table: list[tuple[int, float]] = []
    
    def add_calibration_point(
        self, 
        raw: int, 
        calibrated: float
    ) -> None:
        """Add calibration point maintaining sorted order."""
        import bisect
        idx = bisect.bisect_left([p[0] for p in self.table], raw)
        self.table.insert(idx, (raw, calibrated))
    
    def lookup(self, raw_reading: int) -> float | None:
        """
        Look up calibrated value for raw sensor reading.
        
        >>> cal = SensorCalibrationTable()
        >>> for i in range(11):
        ...     cal.add_calibration_point(i * 100, i * 1.5)
        >>> cal.lookup(500)
        7.5
        """
        if not self.table:
            return None
        
        n = len(self.table)
        
        # Fibonacci search
        f0, f1, fk = 0, 1, 1
        while fk < n:
            f0, f1 = f1, fk
            fk = f0 + f1
        
        offset = -1
        
        while fk > 1:
            i = offset + f0
            if i >= n:
                i = n - 1
            
            raw, cal = self.table[i]
            
            if raw < raw_reading:
                fk, f1, f0 = f1, f0, f1 - f0
                offset = i
            elif raw > raw_reading:
                fk, f1, f0 = f0, f1 - f0, fk - f1
            else:
                return cal
        
        # Check final position
        if f1 and offset + 1 < n:
            raw, cal = self.table[offset + 1]
            if raw == raw_reading:
                return cal
        
        return None
    
    def interpolate(self, raw_reading: int) -> float:
        """
        Get interpolated calibrated value.
        Finds nearest calibration points and interpolates.
        """
        if not self.table:
            raise ValueError("Empty calibration table")
        
        # Find lower bound using Fibonacci search
        n = len(self.table)
        
        if raw_reading <= self.table[0][0]:
            return self.table[0][1]
        if raw_reading >= self.table[-1][0]:
            return self.table[-1][1]
        
        # Find index where table[i][0] >= raw_reading
        f0, f1, fk = 0, 1, 1
        while fk < n:
            f0, f1 = f1, fk
            fk = f0 + f1
        
        offset = -1
        lower_idx = 0
        
        while fk > 1:
            i = offset + f0
            if i >= n:
                i = n - 1
            
            if self.table[i][0] < raw_reading:
                fk, f1, f0 = f1, f0, f1 - f0
                offset = i
                lower_idx = i
            else:
                fk, f1, f0 = f0, f1 - f0, fk - f1
        
        # Interpolate between lower_idx and lower_idx + 1
        raw_lo, cal_lo = self.table[lower_idx]
        raw_hi, cal_hi = self.table[min(lower_idx + 1, n - 1)]
        
        if raw_hi == raw_lo:
            return cal_lo
        
        fraction = (raw_reading - raw_lo) / (raw_hi - raw_lo)
        return cal_lo + fraction * (cal_hi - cal_lo)
```

## Comparison with Other Search Algorithms

| Algorithm | Division | Comparisons | Best Use Case |
|-----------|----------|-------------|---------------|
| Binary | Yes | O(log n) | General purpose |
| Fibonacci | No | O(log n) | Division-costly systems |
| Jump | No | O(√n) | Sequential access |
| Interpolation | Yes | O(log log n) | Uniform data |

## Advantages and Disadvantages

### Advantages
- No division operations required
- O(1) extra space
- Good for sequential access patterns
- Deterministic behavior

### Disadvantages
- Slightly more comparisons than binary search
- More complex implementation
- Fibonacci computation overhead
- Less cache-friendly access pattern

## References

1. [Fibonacci Search - Wikipedia](https://en.wikipedia.org/wiki/Fibonacci_search_technique)
2. Ferguson, D. E. "Fibonaccian Searching" (1960)
3. Knuth, D. E. "The Art of Computer Programming, Vol. 3"
4. [Fibonacci Numbers in Computing](https://en.wikipedia.org/wiki/Fibonacci_number)

## See Also

- [Binary Search](binary_search.md) - Standard division-based search
- [Jump Search](jump_search.md) - Another division-free option
- [Exponential Search](exponential_search.md) - For unbounded arrays
- [Interpolation Search](interpolation_search.md) - For uniform distributions
