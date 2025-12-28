# Comb Sort

## Overview

| Property | Value |
|----------|-------|
| **Category** | Sorting Algorithm |
| **Type** | Comparison-Based (Exchange Sort) |
| **Data Structure** | Array |
| **Space Complexity** | O(1) |
| **Stable** | No |
| **In-Place** | Yes |
| **Adaptive** | Yes |

---

## Mathematical Foundation

### Definition

Comb Sort is an improvement over Bubble Sort that uses a variable gap to eliminate "turtles" (small values near the end of the list). It starts with a large gap and shrinks it by a **shrink factor** until the gap becomes 1, at which point it becomes equivalent to Bubble Sort.

### Historical Context

- Originally designed by **Włodzimierz Dobosiewicz** in 1980
- Rediscovered by **Stephen Lacey** and **Richard Box** in 1991
- Published in *Byte Magazine*, April 1991

### The Shrink Factor

The **optimal shrink factor** has been empirically determined to be approximately:

$$k = 1.3$$

This value was found through extensive experiments. The theoretical basis:

$$k = \frac{1}{1 - e^{-\phi}} \approx 1.247$$

where $\phi = \frac{1 + \sqrt{5}}{2}$ is the golden ratio.

**Commonly used values:**
- 1.3 (most common, good balance)
- 1.247330950103979 (theoretical optimum)
- 10/13 ≈ 1.3 (Lacey and Box recommendation)

### Gap Sequence

Starting with $gap_0 = n$:

$$gap_{i+1} = \max\left(\left\lfloor \frac{gap_i}{k} \right\rfloor, 1\right)$$

**Example gap sequence for n = 100, k = 1.3:**
$$100 \rightarrow 76 \rightarrow 58 \rightarrow 44 \rightarrow 33 \rightarrow 25 \rightarrow 19 \rightarrow 14 \rightarrow 10 \rightarrow 7 \rightarrow 5 \rightarrow 3 \rightarrow 2 \rightarrow 1$$

### Turtle Problem Analysis

**In Bubble Sort:**
- "Rabbits" (large values) move quickly to end (one position per comparison)
- "Turtles" (small values at end) move slowly to beginning

**In Comb Sort:**
- Large gaps eliminate turtles early
- By the time gap = 1, most disorder is resolved

### Mathematical Comparison

Let $T(n)$ be the number of comparisons:

**Bubble Sort:**
$$T_{bubble}(n) = O(n^2)$$

**Comb Sort (empirical):**
$$T_{comb}(n) = O(n \log n) \text{ average}$$
$$T_{comb}(n) = O(n^2) \text{ worst case}$$

---

## Pseudocode

```
COMB-SORT(A):
    Input: Array A of n elements
    Output: Sorted array A (in-place)
    
    SHRINK_FACTOR ← 1.3
    gap ← n
    sorted ← false
    
    while not sorted:
        // Shrink gap
        gap ← floor(gap / SHRINK_FACTOR)
        if gap <= 1:
            gap ← 1
            sorted ← true  // Will exit if no swaps
        
        // Single "comb" over the array
        i ← 0
        while i + gap < n:
            if A[i] > A[i + gap]:
                swap(A[i], A[i + gap])
                sorted ← false  // Continue sorting
            i ← i + 1
    
    return A
```

### With Gap 11 Optimization

A known improvement: when gap is 9 or 10, set it to 11 instead:

```
COMB-SORT-OPTIMIZED(A):
    gap ← n
    sorted ← false
    
    while not sorted:
        gap ← floor(gap / 1.3)
        
        // Rule of 11 optimization
        if gap == 9 or gap == 10:
            gap ← 11
        
        if gap < 1:
            gap ← 1
            sorted ← true
        
        // ... rest same as above
```

---

## Complexity Analysis

### Time Complexity

| Case | Complexity | Condition |
|------|------------|-----------|
| **Best** | $O(n \log n)$ | Few turtles |
| **Average** | $O(n^2 / 2^p)$ | Typical case |
| **Worst** | $O(n^2)$ | Many inversions at gap 1 |

**Where** $p$ is the number of gap passes where swaps occur.

### Empirical Analysis

For random data:
- Average comparisons: $\approx 2.5n \log n$
- Approximately 2.5x more comparisons than optimal
- But significantly fewer than Bubble Sort's $\frac{n^2}{2}$

### Space Complexity

| Type | Complexity |
|------|------------|
| Auxiliary | $O(1)$ |
| Total | $O(n)$ (input only) |

### Comparison with Related Sorts

| Algorithm | Average | Worst | Space | Stable |
|-----------|---------|-------|-------|--------|
| Comb Sort | $O(n \log n)$* | $O(n^2)$ | $O(1)$ | No |
| Bubble Sort | $O(n^2)$ | $O(n^2)$ | $O(1)$ | Yes |
| Shell Sort | $O(n^{4/3})$ | $O(n^{3/2})$ | $O(1)$ | No |
| Quick Sort | $O(n \log n)$ | $O(n^2)$ | $O(\log n)$ | No |

*Empirically observed, not guaranteed

---

## Visual Representation

### Algorithm Flow

```mermaid
flowchart TD
    A[Initialize gap = n] --> B{gap > 1?}
    B -->|Yes| C[gap = floor gap/1.3]
    B -->|No| D{Any swaps in last pass?}
    D -->|No| E[Done: Array sorted]
    D -->|Yes| B
    C --> F[sorted = gap <= 1]
    F --> G[Compare elements gap apart]
    G --> H{A[i] > A[i+gap]?}
    H -->|Yes| I[Swap and mark unsorted]
    H -->|No| J[Move to next pair]
    I --> J
    J --> K{More pairs?}
    K -->|Yes| G
    K -->|No| B
```

### Gap Shrinking Visualization

```
Array size: 13 elements
Gap sequence with shrink factor 1.3:

Gap 13: Compare [0,13] → out of bounds, skip
        ↓
Gap 10: ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ● ● ●
        │───────────────────│
        
Gap 7:  ○ ○ ○ ○ ○ ○ ○ ● ● ● ● ● ●
        │─────────────│
        
Gap 5:  ○ ○ ○ ○ ○ ● ● ● ● ● ● ● ●
        │───────│
        
Gap 3:  ○ ○ ○ ● ● ● ● ● ● ● ● ● ●
        │───│
        
Gap 2:  ○ ○ ● ● ● ● ● ● ● ● ● ● ●
        │─│
        
Gap 1:  ○ ● ● ● ● ● ● ● ● ● ● ● ●  (Bubble Sort)
        │
```

### Step-by-Step Example

```
Initial: [8, 4, 1, 56, 3, -44, 23, -6, 28, 0]
n = 10

Gap = 10 → 10/1.3 = 7
Comparing positions 0-7, 1-8, 2-9:
  [8] vs [-6]: swap → [-6, 4, 1, 56, 3, -44, 23, 8, 28, 0]
  [4] vs [28]: ok
  [1] vs [0]: swap → [-6, 4, 0, 56, 3, -44, 23, 8, 28, 1]

Gap = 7 → 7/1.3 = 5
Comparing positions 0-5, 1-6, 2-7, 3-8, 4-9:
  [-6] vs [-44]: ok
  [4] vs [23]: ok
  [0] vs [8]: ok
  [56] vs [28]: swap → [-6, 4, 0, 28, 3, -44, 23, 8, 56, 1]
  [3] vs [1]: swap → [-6, 4, 0, 28, 1, -44, 23, 8, 56, 3]

... continue until gap = 1 and no swaps ...

Final: [-44, -6, 0, 1, 3, 4, 8, 23, 28, 56]
```

---

## Implementation Details

### Python Implementation

```python
def comb_sort(data: list) -> list:
    """
    Pure implementation of comb sort algorithm in Python.
    
    :param data: mutable collection with comparable items
    :return: the same collection in ascending order
    
    Examples:
    >>> comb_sort([0, 5, 3, 2, 2])
    [0, 2, 2, 3, 5]
    >>> comb_sort([])
    []
    >>> comb_sort([99, 45, -7, 8, 2, 0, -15, 3])
    [-15, -7, 0, 2, 3, 8, 45, 99]
    """
    shrink_factor = 1.3
    gap = len(data)
    completed = False
    
    while not completed:
        # Update gap for next comb
        gap = int(gap / shrink_factor)
        if gap <= 1:
            completed = True
        
        # Single comb over the array
        index = 0
        while index + gap < len(data):
            if data[index] > data[index + gap]:
                data[index], data[index + gap] = data[index + gap], data[index]
                completed = False
            index += 1
    
    return data
```

### Optimized Version

```python
def comb_sort_optimized(data: list) -> list:
    """
    Optimized comb sort with gap 11 rule.
    """
    n = len(data)
    gap = n
    shrink = 1.3
    is_sorted = False
    
    while not is_sorted:
        gap = int(gap / shrink)
        
        # Gap 11 optimization
        if gap in (9, 10):
            gap = 11
        
        if gap <= 1:
            gap = 1
            is_sorted = True
        
        i = 0
        while i + gap < n:
            if data[i] > data[i + gap]:
                data[i], data[i + gap] = data[i + gap], data[i]
                is_sorted = False
            i += 1
    
    return data
```

### Edge Cases

| Case | Handling |
|------|----------|
| Empty array | Return empty |
| Single element | Return as-is |
| Two elements | One comparison |
| Already sorted | One complete pass |
| Reverse sorted | Multiple passes |

---

## Real-World Applications

### 1. **General-Purpose Sorting in Embedded Systems**

**Use Case**: Sorting in memory-constrained environments.

```python
class EmbeddedSorter:
    """
    Comb sort for embedded systems:
    - O(1) extra memory
    - Better than O(n²) average case
    - Simple implementation
    """
    
    @staticmethod
    def sort(data: list, shrink: float = 1.3) -> list:
        n = len(data)
        gap = n
        is_sorted = False
        
        while not is_sorted:
            gap = max(1, int(gap / shrink))
            is_sorted = (gap == 1)
            
            for i in range(n - gap):
                if data[i] > data[i + gap]:
                    data[i], data[i + gap] = data[i + gap], data[i]
                    is_sorted = False
        
        return data
```

### 2. **Sorting Nearly-Sorted Data**

**Use Case**: Re-sorting data after minor modifications.

```python
class IncrementalDataSorter:
    """
    Comb sort is efficient when data is nearly sorted.
    The large initial gap quickly fixes far-away inversions.
    """
    
    def __init__(self):
        self.data = []
    
    def add_batch(self, new_items):
        """Add new items and resort."""
        self.data.extend(new_items)
        return self._comb_sort()
    
    def _comb_sort(self):
        gap = len(self.data)
        shrink = 1.3
        sorted_flag = False
        
        while not sorted_flag:
            gap = max(1, int(gap / shrink))
            sorted_flag = (gap == 1)
            
            for i in range(len(self.data) - gap):
                if self.data[i] > self.data[i + gap]:
                    self.data[i], self.data[i + gap] = \
                        self.data[i + gap], self.data[i]
                    sorted_flag = False
        
        return self.data
```

### 3. **Parallel Processing Preparation**

**Use Case**: Pre-sorting data before parallel merge operations.

```python
def parallel_friendly_presort(chunks: list[list]) -> list[list]:
    """
    Pre-sort chunks using Comb Sort before parallel merge.
    Comb Sort's simple inner loop is easy to optimize.
    """
    sorted_chunks = []
    
    for chunk in chunks:
        # Comb sort each chunk
        gap = len(chunk)
        done = False
        
        while not done:
            gap = max(1, int(gap / 1.3))
            done = (gap == 1)
            
            for i in range(len(chunk) - gap):
                if chunk[i] > chunk[i + gap]:
                    chunk[i], chunk[i + gap] = chunk[i + gap], chunk[i]
                    done = False
        
        sorted_chunks.append(chunk)
    
    return sorted_chunks
```

### 4. **Game Development - Sorting Game Objects**

**Use Case**: Sorting game objects by depth/priority for rendering.

```python
class GameObjectSorter:
    """
    Sort game objects by z-depth for rendering.
    Comb sort is good because:
    - Simple implementation (less bug-prone in game code)
    - Good average performance
    - In-place (memory efficient)
    """
    
    def sort_by_depth(self, objects: list):
        """Sort game objects by z-depth for proper rendering order."""
        gap = len(objects)
        done = False
        
        while not done:
            gap = max(1, int(gap / 1.3))
            done = (gap == 1)
            
            for i in range(len(objects) - gap):
                if objects[i].z_depth > objects[i + gap].z_depth:
                    objects[i], objects[i + gap] = objects[i + gap], objects[i]
                    done = False
        
        return objects
```

### 5. **Data Deduplication Preparation**

**Use Case**: Sorting data before removing duplicates.

```python
def sort_and_dedupe(data: list) -> list:
    """
    Sort data using Comb Sort, then deduplicate.
    Useful for data cleaning pipelines.
    """
    # Comb sort
    gap = len(data)
    done = False
    
    while not done:
        gap = max(1, int(gap / 1.3))
        done = (gap == 1)
        
        for i in range(len(data) - gap):
            if data[i] > data[i + gap]:
                data[i], data[i + gap] = data[i + gap], data[i]
                done = False
    
    # Remove duplicates (consecutive duplicates after sorting)
    if not data:
        return []
    
    result = [data[0]]
    for item in data[1:]:
        if item != result[-1]:
            result.append(item)
    
    return result
```

---

## When to Use Comb Sort

### ✅ Ideal Scenarios

1. **Simple implementation needed** - Easier than quicksort
2. **Memory constrained** - Only $O(1)$ extra space
3. **Nearly sorted data** - Adaptive performance
4. **Avoiding worst-case quicksort** - More predictable
5. **Embedded systems** - Simple and efficient

### ❌ Avoid When

1. **Stability required** - Comb sort is not stable
2. **Guaranteed $O(n \log n)$** needed - Use merge sort
3. **Very large datasets** - Quicksort usually faster
4. **Cache efficiency critical** - Larger gaps cause cache misses

---

## Variations and Improvements

### Combsort11

Uses gap sequence that avoids gaps of 9 and 10:

```python
def combsort11(data):
    gap = len(data)
    done = False
    
    while not done:
        gap = int(gap * 10 / 13)  # Equivalent to /1.3
        
        if gap in (9, 10):
            gap = 11
        elif gap < 1:
            gap = 1
        
        done = (gap == 1)
        # ... rest unchanged
```

### Comparison of Variants

| Variant | Gap Formula | Performance |
|---------|-------------|-------------|
| Original | gap / 1.3 | Good |
| Combsort11 | gap / 1.3, skip 9,10 | Slightly better |
| Theoretical | gap / 1.247 | Similar |

---

## References

1. Dobosiewicz, W. (1980). "An efficient variation of bubble sort"
2. Lacey, S. & Box, R. (1991). "A Fast, Easy Sort". *Byte Magazine*
3. [Wikipedia: Comb Sort](https://en.wikipedia.org/wiki/Comb_sort)

---

## See Also

- [Bubble Sort](bubble_sort.md) - Base algorithm
- [Shell Sort](shell_sort.md) - Similar gap-based approach on insertion sort
- [Cocktail Shaker Sort](cocktail_shaker_sort.md) - Another bubble sort improvement

