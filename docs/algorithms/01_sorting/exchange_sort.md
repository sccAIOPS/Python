# Exchange Sort

## Overview

| Property | Value |
|----------|-------|
| **Category** | Sorting Algorithm |
| **Type** | Comparison-Based (Exchange Sort) |
| **Data Structure** | Array |
| **Space Complexity** | O(1) |
| **Stable** | No |
| **In-Place** | Yes |
| **Adaptive** | No |

---

## Mathematical Foundation

### Definition

**Exchange Sort** is a simple comparison-based sorting algorithm where each element is compared with every subsequent element, and swaps are performed when elements are out of order. It's conceptually similar to Bubble Sort but differs in comparison pattern.

### Algorithm Principle

For each position $i$ from $0$ to $n-2$:
- Compare $a_i$ with all elements $a_j$ where $j > i$
- If $a_j < a_i$, swap them

After iteration $i$ completes, position $i$ contains the $i$-th smallest element.

### Mathematical Formulation

The algorithm ensures that after processing position $i$:
$$a_i = \min\{a_i, a_{i+1}, \ldots, a_{n-1}\}$$

This is achieved by iterating through all pairs $(i, j)$ where $i < j$.

### Comparison Pattern

Unlike Bubble Sort which compares adjacent elements, Exchange Sort compares:
- Element at position $i$ with all elements at positions $i+1, i+2, \ldots, n-1$

**Number of Comparisons:**
$$C(n) = \sum_{i=0}^{n-2} (n-1-i) = \sum_{k=1}^{n-1} k = \frac{n(n-1)}{2}$$

### Difference from Bubble Sort

| Feature | Exchange Sort | Bubble Sort |
|---------|---------------|-------------|
| Comparison pattern | $i$ vs all $j > i$ | Adjacent pairs |
| Element movement | Can "jump" positions | Moves one position at a time |
| After iteration $i$ | Position $i$ is final | Largest $i$ elements in place |
| Early termination | No | Yes (if no swaps) |

---

## Pseudocode

```
EXCHANGE-SORT(A):
    Input: Array A of n elements
    Output: Sorted array A (in-place)
    
    n ← length(A)
    
    for i ← 0 to n - 2:
        for j ← i + 1 to n - 1:
            if A[j] < A[i]:
                swap A[i] and A[j]
    
    return A
```

### Step-by-Step Trace

```
Input: [5, 4, 3, 2, 1]

i = 0:
  j = 1: 4 < 5 → swap → [4, 5, 3, 2, 1]
  j = 2: 3 < 4 → swap → [3, 5, 4, 2, 1]
  j = 3: 2 < 3 → swap → [2, 5, 4, 3, 1]
  j = 4: 1 < 2 → swap → [1, 5, 4, 3, 2]
  Position 0 now has minimum (1)

i = 1:
  j = 2: 4 < 5 → swap → [1, 4, 5, 3, 2]
  j = 3: 3 < 4 → swap → [1, 3, 5, 4, 2]
  j = 4: 2 < 3 → swap → [1, 2, 5, 4, 3]
  Position 1 now has second minimum (2)

i = 2:
  j = 3: 4 < 5 → swap → [1, 2, 4, 5, 3]
  j = 4: 3 < 4 → swap → [1, 2, 3, 5, 4]
  Position 2 now has third minimum (3)

i = 3:
  j = 4: 4 < 5 → swap → [1, 2, 3, 4, 5]
  Position 3 now has fourth minimum (4)

Output: [1, 2, 3, 4, 5]
```

---

## Complexity Analysis

### Time Complexity

| Case | Complexity | Notes |
|------|------------|-------|
| **Best** | $O(n^2)$ | Still compares all pairs |
| **Average** | $O(n^2)$ | Always same comparisons |
| **Worst** | $O(n^2)$ | Always same comparisons |

**Number of Comparisons (always):**
$$C(n) = \frac{n(n-1)}{2} = O(n^2)$$

**Number of Swaps:**
- Best case (sorted): 0 swaps
- Worst case (reverse sorted): $\frac{n(n-1)}{2}$ swaps
- Average case: $\approx \frac{n(n-1)}{4}$ swaps

### Space Complexity

| Component | Space |
|-----------|-------|
| Temp variable for swap | $O(1)$ |
| **Total** | $O(1)$ |

---

## Visual Representation

### Algorithm Flow

```mermaid
flowchart TD
    A[Start] --> B[i = 0]
    B --> C{i < n-1?}
    C -->|Yes| D[j = i + 1]
    D --> E{j < n?}
    E -->|Yes| F{A[j] < A[i]?}
    F -->|Yes| G[Swap A[i] and A[j]]
    F -->|No| H[j++]
    G --> H
    H --> E
    E -->|No| I[i++]
    I --> C
    C -->|No| J[Return sorted A]
```

### Sorting Visualization

```
Initial: [5, 4, 3, 2, 1]

Round i=0: Finding minimum for position 0
  Compare 5 with [4,3,2,1]:
  [5, 4, 3, 2, 1]
   ↑  ↑ swap
  [4, 5, 3, 2, 1]
   ↑     ↑ swap
  [3, 5, 4, 2, 1]
   ↑        ↑ swap
  [2, 5, 4, 3, 1]
   ↑           ↑ swap
  [1, 5, 4, 3, 2] ← Position 0 final

Round i=1: Finding minimum for position 1
  Compare 5 with [4,3,2]:
  [1, 5, 4, 3, 2]
      ↑  ↑ swap
  [1, 4, 5, 3, 2]
      ↑     ↑ swap
  [1, 3, 5, 4, 2]
      ↑        ↑ swap
  [1, 2, 5, 4, 3] ← Position 1 final

Round i=2: Finding minimum for position 2
  [1, 2, 5, 4, 3]
         ↑  ↑ swap
  [1, 2, 4, 5, 3]
         ↑     ↑ swap
  [1, 2, 3, 5, 4] ← Position 2 final

Round i=3: Finding minimum for position 3
  [1, 2, 3, 5, 4]
            ↑  ↑ swap
  [1, 2, 3, 4, 5] ← Position 3 final

Final: [1, 2, 3, 4, 5]
```

---

## Implementation Details

### Python Implementation

```python
def exchange_sort(numbers: list[int]) -> list[int]:
    """
    Uses exchange sort to sort a list of numbers.
    
    >>> exchange_sort([5, 4, 3, 2, 1])
    [1, 2, 3, 4, 5]
    >>> exchange_sort([-1, -2, -3])
    [-3, -2, -1]
    >>> exchange_sort([1, 2, 3, 4, 5])
    [1, 2, 3, 4, 5]
    >>> exchange_sort([0, 10, -2, 5, 3])
    [-2, 0, 3, 5, 10]
    >>> exchange_sort([])
    []
    """
    numbers_length = len(numbers)
    for i in range(numbers_length):
        for j in range(i + 1, numbers_length):
            if numbers[j] < numbers[i]:
                numbers[i], numbers[j] = numbers[j], numbers[i]
    return numbers
```

### Generic Implementation

```python
from typing import TypeVar, Callable

T = TypeVar('T')


def exchange_sort_generic(
    arr: list[T],
    key: Callable[[T], any] = lambda x: x,
    reverse: bool = False
) -> list[T]:
    """
    Generic exchange sort with custom key and order.
    
    >>> exchange_sort_generic([3, 1, 2])
    [1, 2, 3]
    >>> exchange_sort_generic([3, 1, 2], reverse=True)
    [3, 2, 1]
    >>> exchange_sort_generic(['banana', 'apple', 'cherry'], key=len)
    ['apple', 'banana', 'cherry']
    """
    n = len(arr)
    for i in range(n):
        for j in range(i + 1, n):
            should_swap = key(arr[j]) > key(arr[i]) if reverse else key(arr[j]) < key(arr[i])
            if should_swap:
                arr[i], arr[j] = arr[j], arr[i]
    return arr
```

### Counting Comparisons and Swaps

```python
def exchange_sort_with_metrics(arr: list) -> tuple[list, int, int]:
    """
    Exchange sort with comparison and swap counting.
    
    >>> arr, comps, swaps = exchange_sort_with_metrics([3, 2, 1])
    >>> arr
    [1, 2, 3]
    >>> comps
    3
    """
    arr = list(arr)
    n = len(arr)
    comparisons = 0
    swaps = 0
    
    for i in range(n):
        for j in range(i + 1, n):
            comparisons += 1
            if arr[j] < arr[i]:
                arr[i], arr[j] = arr[j], arr[i]
                swaps += 1
    
    return arr, comparisons, swaps
```

---

## Real-World Applications

### 1. **Finding Minimum Elements**

**Use Case**: The algorithm naturally finds minimums in each pass.

```python
def find_k_smallest(arr: list, k: int) -> list:
    """
    Find k smallest elements using partial exchange sort.
    Only need first k iterations.
    
    >>> find_k_smallest([5, 2, 8, 1, 9, 3], 3)
    [1, 2, 3]
    """
    arr = list(arr)
    n = len(arr)
    
    for i in range(min(k, n)):
        for j in range(i + 1, n):
            if arr[j] < arr[i]:
                arr[i], arr[j] = arr[j], arr[i]
    
    return arr[:k]
```

### 2. **Simple Hardware Implementations**

**Use Case**: The algorithm's simplicity makes it suitable for hardware.

```python
class HardwareExchangeSort:
    """
    Simulates hardware exchange sort implementation.
    Simple control logic, minimal state.
    """
    
    def __init__(self, array_size: int):
        self.array_size = array_size
        self.registers = [0] * array_size
        self.cycles = 0
    
    def load(self, data: list):
        """Load data into registers."""
        for i, val in enumerate(data[:self.array_size]):
            self.registers[i] = val
    
    def sort(self):
        """Sort using exchange sort logic."""
        n = min(len(self.registers), self.array_size)
        
        for i in range(n):
            for j in range(i + 1, n):
                self.cycles += 1  # One cycle per comparison
                if self.registers[j] < self.registers[i]:
                    # Swap (additional cycle)
                    self.cycles += 1
                    self.registers[i], self.registers[j] = (
                        self.registers[j], self.registers[i]
                    )
        
        return self.registers
    
    def get_cycles(self):
        """Return total cycles used."""
        return self.cycles
```

### 3. **Educational Demonstration**

**Use Case**: Teaching basic sorting concepts.

```python
def visualize_exchange_sort(arr: list) -> list:
    """
    Visualize exchange sort step by step.
    
    >>> visualize_exchange_sort([4, 2, 3, 1])  # doctest: +SKIP
    """
    arr = list(arr)
    n = len(arr)
    
    print(f"Initial: {arr}")
    print("=" * 40)
    
    for i in range(n):
        print(f"\nPosition {i}: Finding min for this position")
        for j in range(i + 1, n):
            print(f"  Compare arr[{i}]={arr[i]} with arr[{j}]={arr[j]}", end="")
            if arr[j] < arr[i]:
                arr[i], arr[j] = arr[j], arr[i]
                print(f" → Swap → {arr}")
            else:
                print(" → No swap")
        print(f"  Position {i} final value: {arr[i]}")
    
    print("=" * 40)
    print(f"Sorted: {arr}")
    return arr
```

### 4. **Sorting Small Fixed-Size Collections**

**Use Case**: When simplicity matters more than efficiency.

```python
def sort_rgb(r: int, g: int, b: int) -> tuple:
    """
    Sort RGB values using exchange sort.
    For 3 elements, exchange sort is reasonable.
    
    >>> sort_rgb(100, 50, 200)
    (50, 100, 200)
    """
    values = [r, g, b]
    # Exchange sort for 3 elements
    for i in range(3):
        for j in range(i + 1, 3):
            if values[j] < values[i]:
                values[i], values[j] = values[j], values[i]
    return tuple(values)
```

### 5. **Network Packet Ordering**

**Use Case**: Simple packet reordering in network buffers.

```python
class PacketBuffer:
    """
    Simple packet buffer that orders packets by sequence number.
    Uses exchange sort for small buffer sizes.
    """
    
    def __init__(self, max_size: int = 10):
        self.buffer = []
        self.max_size = max_size
    
    def add_packet(self, seq_num: int, data: bytes):
        """Add packet to buffer."""
        if len(self.buffer) < self.max_size:
            self.buffer.append((seq_num, data))
    
    def get_ordered_packets(self):
        """
        Get packets in sequence order.
        Uses exchange sort for small buffer.
        """
        # Exchange sort by sequence number
        n = len(self.buffer)
        for i in range(n):
            for j in range(i + 1, n):
                if self.buffer[j][0] < self.buffer[i][0]:
                    self.buffer[i], self.buffer[j] = self.buffer[j], self.buffer[i]
        
        return [data for _, data in self.buffer]
```

---

## Comparison with Related Algorithms

| Algorithm | Comparisons | Swaps (worst) | Early Termination | Stable |
|-----------|-------------|---------------|-------------------|--------|
| Exchange Sort | $n(n-1)/2$ | $n(n-1)/2$ | No | No |
| Bubble Sort | $n(n-1)/2$ | $n(n-1)/2$ | Yes | Yes |
| Selection Sort | $n(n-1)/2$ | $n-1$ | No | No |

### Key Differences

1. **Exchange Sort vs Selection Sort**:
   - Exchange swaps immediately when finding smaller element
   - Selection finds minimum first, then swaps once
   - Selection has fewer swaps

2. **Exchange Sort vs Bubble Sort**:
   - Exchange compares non-adjacent elements
   - Bubble only compares adjacent elements
   - Bubble can terminate early

---

## Optimizations

### 1. Track If Sorted

```python
def exchange_sort_optimized(arr: list) -> list:
    """
    Exchange sort with early termination check.
    """
    n = len(arr)
    
    for i in range(n):
        swapped = False
        for j in range(i + 1, n):
            if arr[j] < arr[i]:
                arr[i], arr[j] = arr[j], arr[i]
                swapped = True
        
        # If we've processed all positions without swaps,
        # remaining elements are already in order
        if not swapped and i > 0:
            # Check if remaining is sorted
            is_sorted = all(arr[k] <= arr[k+1] for k in range(i, n-1))
            if is_sorted:
                break
    
    return arr
```

### 2. Find Minimum Index First

```python
def exchange_sort_minimum_first(arr: list) -> list:
    """
    Variant that finds minimum index first, then swaps once.
    This is essentially Selection Sort.
    """
    n = len(arr)
    
    for i in range(n - 1):
        min_idx = i
        for j in range(i + 1, n):
            if arr[j] < arr[min_idx]:
                min_idx = j
        
        if min_idx != i:
            arr[i], arr[min_idx] = arr[min_idx], arr[i]
    
    return arr
```

---

## References

1. [Wikipedia: Exchange Sort](https://en.wikipedia.org/wiki/Sorting_algorithm#Exchange_sort)
2. Knuth, D. E. "The Art of Computer Programming, Volume 3"

---

## See Also

- [Bubble Sort](bubble_sort.md) - Similar exchange-based algorithm
- [Selection Sort](selection_sort.md) - Optimized single-swap version
- [Insertion Sort](insertion_sort.md) - Another O(n²) algorithm

