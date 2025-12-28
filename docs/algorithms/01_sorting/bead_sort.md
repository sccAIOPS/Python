# Bead Sort (Gravity Sort)

## Overview

| Property | Value |
|----------|-------|
| **Category** | Sorting Algorithm |
| **Type** | Non-Comparison-Based (Natural Sort) |
| **Data Structure** | Array/Grid |
| **Space Complexity** | O(n × max) |
| **Stable** | No |
| **In-Place** | Yes (with constraints) |
| **Adaptive** | No |

---

## Mathematical Foundation

### Definition

**Bead Sort** (also known as **Gravity Sort**) is a natural sorting algorithm that simulates gravity causing beads to fall on vertical rods. Each number is represented as beads on horizontal rows, and when gravity is applied, larger values (more beads) settle at the bottom.

### Physical Analogy

Imagine an abacus laid flat:
- Each row represents a number
- Beads fill from left to right (number of beads = value)
- Apply "gravity" - beads fall down through columns
- Count beads in each row = sorted values

### Algorithm Principle

1. **Setup**: Create grid with $n$ rows (one per number), $max$ columns
2. **Initialize**: Place $a_i$ beads in row $i$
3. **Apply Gravity**: In each column, count beads and let them "fall"
4. **Read Result**: Count beads in each row

### Mathematical Formulation

Given array $A = [a_0, a_1, \ldots, a_{n-1}]$ with max value $m$:

**Grid Construction:**
$$G[i][j] = \begin{cases} 1 & \text{if } j < a_i \\ 0 & \text{otherwise} \end{cases}$$

**Gravity Application (per column $j$):**
$$\text{count}_j = \sum_{i=0}^{n-1} G[i][j]$$

After gravity, bottom $count_j$ rows have bead in column $j$.

**Reading Sorted Values:**
$$a'_i = \sum_{j=0}^{m-1} G'[i][j]$$

### Constraint

**Bead Sort only works for non-negative integers!**

---

## Pseudocode

```
BEAD-SORT(A):
    Input: Array A of n non-negative integers
    Output: Sorted array A (in-place)
    
    if any element in A is negative:
        raise Error("Only non-negative integers allowed")
    
    n ← length(A)
    max_val ← maximum(A)
    
    // Simulate gravity using pairwise comparisons
    for pass ← 0 to n - 1:
        for i ← 0 to n - 2:
            // Compare adjacent elements
            if A[i] > A[i + 1]:
                // Transfer "excess beads" down
                diff ← A[i] - A[i + 1]
                A[i] ← A[i] - diff  // but this makes them equal
                A[i + 1] ← A[i + 1] + diff
                // Simplified: just swap difference
                A[i], A[i + 1] ← A[i + 1], A[i]
    
    return A
```

### Grid-Based Implementation

```
BEAD-SORT-GRID(A):
    Input: Array A of n non-negative integers
    Output: Sorted array A
    
    n ← length(A)
    max_val ← maximum(A)
    
    // Create grid
    grid ← n × max_val matrix of zeros
    
    // Place beads
    for i ← 0 to n - 1:
        for j ← 0 to A[i] - 1:
            grid[i][j] ← 1
    
    // Apply gravity (column by column)
    for j ← 0 to max_val - 1:
        count ← sum of grid[*][j]  // count beads in column
        // Place beads at bottom
        for i ← n - 1 down to n - count:
            grid[i][j] ← 1
        for i ← 0 to n - count - 1:
            grid[i][j] ← 0
    
    // Read sorted values
    for i ← 0 to n - 1:
        A[i] ← sum of grid[i][*]  // count beads in row
    
    return A
```

---

## Complexity Analysis

### Time Complexity

| Case | Complexity | Notes |
|------|------------|-------|
| **Best** | $O(n)$ | Already sorted |
| **Average** | $O(n)$ | With parallel column processing |
| **Worst** | $O(n)$ | Parallel; $O(n \times max)$ sequential |

**Sequential Analysis:**
- Number of passes: $O(n)$
- Operations per pass: $O(n)$
- Total: $O(n^2)$ or $O(n \times \max)$ depending on implementation

**Parallel Analysis (theoretical):**
- All columns process simultaneously: $O(n)$

### Space Complexity

| Component | Space |
|-----------|-------|
| Grid (if used) | $O(n \times \max)$ |
| In-place variant | $O(1)$ |

### Important Constraint

The algorithm's efficiency depends heavily on the **maximum value**:
- If $\max = O(n)$: Total $O(n^2)$
- If $\max = O(1)$: Total $O(n)$
- If $\max >> n$: Impractical due to space

---

## Visual Representation

### Algorithm Flow

```mermaid
flowchart TD
    A[Input: Non-negative integers] --> B[Create bead grid]
    B --> C[Place beads: row i has A[i] beads]
    C --> D[Apply gravity to each column]
    D --> E[Beads fall to bottom]
    E --> F[Count beads in each row]
    F --> G[Output: Sorted array]
```

### Bead Grid Example

```
Input: [4, 2, 5, 1, 3]

Initial Grid (5 rows, 5 columns):
         Col: 0  1  2  3  4
Row 0 (4):   ●  ●  ●  ●  ○
Row 1 (2):   ●  ●  ○  ○  ○
Row 2 (5):   ●  ●  ●  ●  ●
Row 3 (1):   ●  ○  ○  ○  ○
Row 4 (3):   ●  ●  ●  ○  ○

Apply Gravity (beads fall):

Column 0: 5 beads → all rows get 1
Column 1: 4 beads → rows 1-4 get 1, row 0 gets 0
Column 2: 3 beads → rows 2-4 get 1
Column 3: 2 beads → rows 3-4 get 1
Column 4: 1 bead  → row 4 gets 1

After Gravity:
         Col: 0  1  2  3  4
Row 0 (1):   ●  ○  ○  ○  ○  → 1
Row 1 (2):   ●  ●  ○  ○  ○  → 2
Row 2 (3):   ●  ●  ●  ○  ○  → 3
Row 3 (4):   ●  ●  ●  ●  ○  → 4
Row 4 (5):   ●  ●  ●  ●  ●  → 5

Output: [1, 2, 3, 4, 5]
```

### Physical Simulation

```
Before Gravity:          After Gravity:
    ┌─┬─┬─┬─┬─┐          ┌─┬─┬─┬─┬─┐
A[0]=4: ●─●─●─●─○          ○─○─○─○─○  → 1
    ├─┼─┼─┼─┼─┤          ├─┼─┼─┼─┼─┤
A[1]=2: ●─●─○─○─○          ●─○─○─○─○  → 2
    ├─┼─┼─┼─┼─┤    ↓↓↓   ├─┼─┼─┼─┼─┤
A[2]=5: ●─●─●─●─●  gravity ●─●─○─○─○  → 3
    ├─┼─┼─┼─┼─┤          ├─┼─┼─┼─┼─┤
A[3]=1: ●─○─○─○─○          ●─●─●─○─○  → 4
    ├─┼─┼─┼─┼─┤          ├─┼─┼─┼─┼─┤
A[4]=3: ●─●─●─○─○          ●─●─●─●─●  → 5
    └─┴─┴─┴─┴─┘          └─┴─┴─┴─┴─┘
```

---

## Implementation Details

### Python Implementation

```python
def bead_sort(sequence: list) -> list:
    """
    Bead sort only works for sequences of non-negative integers.
    
    >>> bead_sort([6, 11, 12, 4, 1, 5])
    [1, 4, 5, 6, 11, 12]

    >>> bead_sort([9, 8, 7, 6, 5, 4, 3, 2, 1])
    [1, 2, 3, 4, 5, 6, 7, 8, 9]

    >>> bead_sort([5, 0, 4, 3])
    [0, 3, 4, 5]

    >>> bead_sort([1, .9, 0.0, 0, -1, -.9])
    Traceback (most recent call last):
        ...
    TypeError: Sequence must be list of non-negative integers
    """
    if any(not isinstance(x, int) or x < 0 for x in sequence):
        raise TypeError("Sequence must be list of non-negative integers")
    
    for _ in range(len(sequence)):
        for i, (rod_upper, rod_lower) in enumerate(zip(sequence, sequence[1:])):
            if rod_upper > rod_lower:
                # Beads "fall" from upper rod to lower rod
                sequence[i] -= rod_upper - rod_lower
                sequence[i + 1] += rod_upper - rod_lower
    
    return sequence
```

### Grid-Based Implementation

```python
def bead_sort_grid(arr: list[int]) -> list[int]:
    """
    Bead sort using explicit grid representation.
    
    >>> bead_sort_grid([4, 2, 5, 1, 3])
    [1, 2, 3, 4, 5]
    >>> bead_sort_grid([])
    []
    """
    if not arr:
        return arr
    
    if any(not isinstance(x, int) or x < 0 for x in arr):
        raise TypeError("Only non-negative integers allowed")
    
    n = len(arr)
    max_val = max(arr) if arr else 0
    
    if max_val == 0:
        return arr
    
    # Create grid
    grid = [[0] * max_val for _ in range(n)]
    
    # Place beads
    for i, val in enumerate(arr):
        for j in range(val):
            grid[i][j] = 1
    
    # Apply gravity to each column
    for j in range(max_val):
        # Count beads in column
        count = sum(grid[i][j] for i in range(n))
        
        # Clear column and place beads at bottom
        for i in range(n):
            grid[i][j] = 0
        for i in range(n - count, n):
            grid[i][j] = 1
    
    # Read sorted values
    result = [sum(row) for row in grid]
    
    return result
```

### Optimized Implementation

```python
def bead_sort_optimized(arr: list[int]) -> list[int]:
    """
    Optimized bead sort without explicit grid.
    Uses column counting directly.
    
    >>> bead_sort_optimized([4, 2, 5, 1, 3])
    [1, 2, 3, 4, 5]
    """
    if not arr:
        return arr
    
    if any(not isinstance(x, int) or x < 0 for x in arr):
        raise TypeError("Only non-negative integers allowed")
    
    n = len(arr)
    max_val = max(arr)
    
    # Count beads per column
    # Column j has a bead from row i if arr[i] > j
    result = []
    
    for row in range(n):
        # Count columns that have enough beads to reach this row
        # Row 0 (top) gets fewest beads, row n-1 (bottom) gets most
        count = 0
        for col in range(max_val):
            beads_in_col = sum(1 for x in arr if x > col)
            if beads_in_col >= n - row:  # Bead falls to this row
                count += 1
        result.append(count)
    
    return result
```

---

## Real-World Applications

### 1. **Hardware Sorting**

**Use Case**: Physical computing devices that use gravity.

```python
class GravitySorter:
    """
    Simulates a gravity-based sorting device.
    Conceptual implementation for hardware design.
    """
    
    def __init__(self, num_rods: int, max_beads: int):
        self.num_rods = num_rods
        self.max_beads = max_beads
        self.rods = [[0] * max_beads for _ in range(num_rods)]
    
    def load_values(self, values: list[int]):
        """Load values onto rods (place beads)."""
        for i, val in enumerate(values[:self.num_rods]):
            for j in range(min(val, self.max_beads)):
                self.rods[i][j] = 1
    
    def apply_gravity(self):
        """Simulate gravity - beads fall to bottom."""
        for col in range(self.max_beads):
            # Count beads in column
            count = sum(self.rods[row][col] for row in range(self.num_rods))
            
            # Place at bottom
            for row in range(self.num_rods):
                self.rods[row][col] = 1 if row >= self.num_rods - count else 0
    
    def read_values(self) -> list[int]:
        """Read sorted values from rods."""
        return [sum(rod) for rod in self.rods]
    
    def sort(self, values: list[int]) -> list[int]:
        """Complete sort operation."""
        self.load_values(values)
        self.apply_gravity()
        return self.read_values()
```

### 2. **Educational Visualization**

**Use Case**: Teaching sorting concepts visually.

```python
def visualize_bead_sort(arr: list[int]) -> list[int]:
    """
    Visualize bead sort step by step.
    
    >>> visualize_bead_sort([3, 1, 2])  # doctest: +SKIP
    """
    if any(not isinstance(x, int) or x < 0 for x in arr):
        raise TypeError("Only non-negative integers allowed")
    
    n = len(arr)
    if n == 0:
        return arr
    
    max_val = max(arr)
    
    print("Initial array:", arr)
    print("\nInitial bead grid:")
    print_grid(arr, max_val)
    
    # Simulate passes
    arr = list(arr)
    for pass_num in range(n):
        changed = False
        for i in range(n - 1):
            if arr[i] > arr[i + 1]:
                # Beads fall
                diff = arr[i] - arr[i + 1]
                arr[i] -= diff
                arr[i + 1] += diff
                changed = True
        
        if changed:
            print(f"\nAfter pass {pass_num + 1}:")
            print_grid(arr, max_val)
        else:
            print(f"\nStable after pass {pass_num + 1}")
            break
    
    print("\nSorted array:", arr)
    return arr


def print_grid(arr, max_val):
    """Print bead grid."""
    for i, val in enumerate(arr):
        beads = '●' * val + '○' * (max_val - val)
        print(f"  Row {i} ({val}): {beads}")
```

### 3. **Parallel Processing Demonstration**

**Use Case**: Teaching parallel algorithms.

```python
def parallel_bead_sort_simulation(arr: list[int]) -> list[int]:
    """
    Simulate parallel bead sort where all columns process simultaneously.
    
    In true parallel execution, each column's gravity would be applied
    at the same time, making this O(n) in parallel time.
    """
    from concurrent.futures import ThreadPoolExecutor
    
    if not arr:
        return arr
    
    n = len(arr)
    max_val = max(arr)
    
    # Create grid
    grid = [[1 if j < arr[i] else 0 for j in range(max_val)] for i in range(n)]
    
    def process_column(col):
        """Process one column (can run in parallel)."""
        count = sum(grid[row][col] for row in range(n))
        return [(col, row, 1 if row >= n - count else 0) for row in range(n)]
    
    # Process all columns in parallel
    with ThreadPoolExecutor() as executor:
        results = list(executor.map(process_column, range(max_val)))
    
    # Apply results
    for col_results in results:
        for col, row, val in col_results:
            grid[row][col] = val
    
    # Read result
    return [sum(row) for row in grid]
```

### 4. **Analog Computing**

**Use Case**: Conceptual model for analog computation.

```python
class AnalogBeadSorter:
    """
    Model for analog bead sorting computation.
    
    In an analog computer, this could be implemented with:
    - Physical beads on rods
    - Voltage levels representing bead counts
    - Capacitor-based gravity simulation
    """
    
    def __init__(self, precision: int = 100):
        self.precision = precision  # Maximum value representable
    
    def normalize_input(self, values: list[int]) -> list[int]:
        """Scale values to fit precision."""
        if not values:
            return values
        max_val = max(values)
        if max_val > self.precision:
            scale = self.precision / max_val
            return [int(v * scale) for v in values]
        return values
    
    def sort(self, values: list[int]) -> list[int]:
        """Sort using analog bead sort model."""
        normalized = self.normalize_input(values)
        return bead_sort_grid(normalized)
```

### 5. **Simple Integer Sorting**

**Use Case**: When input is known to be small non-negative integers.

```python
def sort_scores(scores: list[int]) -> list[int]:
    """
    Sort game scores using bead sort.
    Efficient for scores in range [0, 100].
    
    >>> sort_scores([85, 92, 78, 95, 88])
    [78, 85, 88, 92, 95]
    """
    if not all(0 <= s <= 100 for s in scores):
        raise ValueError("Scores must be in range [0, 100]")
    
    return bead_sort_grid(scores)
```

---

## Limitations and Constraints

### ✅ Works Well For

1. **Non-negative integers only**
2. **Small maximum values** (otherwise space explodes)
3. **When parallel processing is available**
4. **Educational/demonstration purposes**

### ❌ Does Not Work For

1. **Negative numbers** - no physical representation
2. **Floating point numbers** - discrete beads required
3. **Large values** - impractical space usage
4. **General purpose sorting** - other algorithms are better

---

## Comparison with Other Algorithms

| Algorithm | Handles Negatives | Handles Floats | Space | Time |
|-----------|-------------------|----------------|-------|------|
| Bead Sort | ❌ | ❌ | $O(n \times max)$ | $O(n)$ parallel |
| Counting Sort | ❌ | ❌ | $O(k)$ | $O(n + k)$ |
| Radix Sort | ❌ | ❌ | $O(n + k)$ | $O(d(n + k))$ |
| Quick Sort | ✅ | ✅ | $O(\log n)$ | $O(n \log n)$ |

---

## Theoretical Significance

Bead Sort is significant because:
1. **Natural algorithm** - uses physical phenomena
2. **Inherently parallel** - O(n) in parallel time
3. **Educational value** - visualizes sorting clearly
4. **Unconventional** - challenges traditional algorithm design

---

## References

1. [Wikipedia: Bead Sort](https://en.wikipedia.org/wiki/Bead_sort)
2. Arulanandham, J. J. "Implementing Bead Sort with P Systems"

---

## See Also

- [Counting Sort](counting_sort.md) - Also for integers
- [Radix Sort](radix_sort.md) - Digit-by-digit sorting
- [Pigeonhole Sort](pigeonhole_sort.md) - Similar constraints

