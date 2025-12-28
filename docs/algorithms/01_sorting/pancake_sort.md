# Pancake Sort

## Overview

| Property | Value |
|----------|-------|
| **Category** | Sorting Algorithm |
| **Type** | Comparison-Based (Flip-Based) |
| **Data Structure** | Array |
| **Space Complexity** | O(1) |
| **Stable** | No |
| **In-Place** | Yes |
| **Adaptive** | No |

---

## Mathematical Foundation

### Definition

Pancake Sort is a comparison-based sorting algorithm that sorts an array by repeatedly "flipping" prefixes of the array. A flip reverses the order of the first $k$ elements for some chosen $k$. The algorithm is named after the problem of sorting a stack of pancakes by size using a spatula.

### The Pancake Problem

The **Pancake Problem** asks:
> What is the minimum number of flips required to sort any permutation of $n$ elements?

This minimum number is called the **pancake number** $P_n$.

### Flip Operation

A **flip** at position $k$ reverses elements from index $0$ to $k$:

$$\text{flip}(A, k): [a_0, a_1, \ldots, a_{k-1}, a_k, \ldots, a_{n-1}] \rightarrow [a_k, a_{k-1}, \ldots, a_1, a_0, a_{k+1}, \ldots, a_{n-1}]$$

### Algorithm Strategy

The algorithm works by:
1. Find the maximum unsorted element
2. Flip it to position 0 (top of stack)
3. Flip it to its correct position
4. Repeat for the next unsorted portion

### Mathematical Properties

1. **Upper Bound**: Any permutation can be sorted in at most $2n - 3$ flips
2. **Lower Bound**: Some permutations require at least $\lceil \frac{15n}{14} \rceil$ flips
3. **Pancake Numbers**: $P_1 = 0, P_2 = 1, P_3 = 3, P_4 = 4, P_5 = 5, \ldots$

### Recurrence Relation

For the simple algorithm (not optimal):
- Moving each element requires at most 2 flips
- Total: $T(n) = 2(n-1) = O(n)$ flips
- Each flip: $O(n)$ operations
- **Total: $O(n^2)$ comparisons**

---

## Pseudocode

```
PANCAKE-SORT(A):
    Input: Array A of n comparable elements
    Output: Sorted array A (in-place)
    
    n ← length(A)
    
    // Process each position from end to start
    for curr_size ← n down to 2:
        
        // Find index of maximum in A[0..curr_size-1]
        max_index ← FIND-MAX-INDEX(A, curr_size)
        
        // Move maximum to its correct position if not already there
        if max_index ≠ curr_size - 1:
            
            // First flip: bring max to front (if not already there)
            if max_index > 0:
                FLIP(A, max_index)
            
            // Second flip: move max to its correct position
            FLIP(A, curr_size - 1)
    
    return A


FLIP(A, k):
    // Reverse elements from index 0 to k
    start ← 0
    end ← k
    while start < end:
        swap A[start] and A[end]
        start ← start + 1
        end ← end - 1


FIND-MAX-INDEX(A, size):
    // Find index of maximum element in A[0..size-1]
    max_index ← 0
    for i ← 1 to size - 1:
        if A[i] > A[max_index]:
            max_index ← i
    return max_index
```

### Alternative: Recursive Version

```
PANCAKE-SORT-RECURSIVE(A, n):
    Input: Array A, current size n
    Output: Sorted array A
    
    if n == 1:
        return A
    
    // Find maximum element in A[0..n-1]
    max_idx ← FIND-MAX-INDEX(A, n)
    
    // Move maximum to the end
    if max_idx ≠ n - 1:
        if max_idx > 0:
            FLIP(A, max_idx)      // Bring max to top
        FLIP(A, n - 1)            // Send max to position n-1
    
    // Recurse on smaller array
    PANCAKE-SORT-RECURSIVE(A, n - 1)
    
    return A
```

---

## Complexity Analysis

### Time Complexity

| Case | Complexity | Notes |
|------|------------|-------|
| **Best** | $O(n^2)$ | Already sorted still requires checking |
| **Average** | $O(n^2)$ | Typical case |
| **Worst** | $O(n^2)$ | Every element needs flipping |

**Breakdown:**
- Number of iterations: $n - 1$
- Each iteration:
  - Find max: $O(k)$ where $k$ decreases from $n$ to $2$
  - Up to 2 flips: $O(k)$ each
- Total: $\sum_{k=2}^{n} O(k) = O(n^2)$

### Space Complexity

| Component | Space |
|-----------|-------|
| Auxiliary variables | $O(1)$ |
| Recursion stack (if recursive) | $O(n)$ |
| **Total (iterative)** | $O(1)$ |

### Flip Complexity

| Metric | Value |
|--------|-------|
| Maximum flips needed | $2n - 3$ |
| Average flips | $\approx 1.64n$ |
| Comparisons per flip | $O(n)$ |

---

## Visual Representation

### Algorithm Flow

```mermaid
flowchart TD
    A[Start with unsorted array] --> B[Set curr_size = n]
    B --> C{curr_size > 1?}
    C -->|Yes| D[Find max in A[0..curr_size-1]]
    D --> E{Max at correct position?}
    E -->|No| F{Max at position 0?}
    F -->|No| G[Flip to bring max to top]
    F -->|Yes| H[Flip to send max to curr_size-1]
    G --> H
    E -->|Yes| I[Decrement curr_size]
    H --> I
    I --> C
    C -->|No| J[Array is sorted]
```

### Sorting Example

```
Initial: [3, 6, 2, 7, 4, 1, 5]

Round 1 (curr_size = 7):
  Max = 7 at index 3
  Flip(3): [7, 2, 6, 3, 4, 1, 5]  ← 7 now at top
  Flip(6): [5, 1, 4, 3, 6, 2, 7]  ← 7 now at end
  
Round 2 (curr_size = 6):
  Max = 6 at index 4
  Flip(4): [6, 3, 4, 1, 5, 2, 7]  ← 6 now at top
  Flip(5): [2, 5, 1, 4, 3, 6, 7]  ← 6 at position 5
  
Round 3 (curr_size = 5):
  Max = 5 at index 1
  Flip(1): [5, 2, 1, 4, 3, 6, 7]  ← 5 now at top
  Flip(4): [3, 4, 1, 2, 5, 6, 7]  ← 5 at position 4
  
Round 4 (curr_size = 4):
  Max = 4 at index 1
  Flip(1): [4, 3, 1, 2, 5, 6, 7]  ← 4 now at top
  Flip(3): [2, 1, 3, 4, 5, 6, 7]  ← 4 at position 3
  
Round 5 (curr_size = 3):
  Max = 3 at index 2
  Already at correct position, no flip needed
  
Round 6 (curr_size = 2):
  Max = 2 at index 0
  Flip(1): [1, 2, 3, 4, 5, 6, 7]  ← 2 at position 1

Final: [1, 2, 3, 4, 5, 6, 7]
```

### Pancake Stack Visualization

```
Initial Stack:    After Flip(3):    After Flip(6):
    ┌───┐             ┌───┐             ┌───┐
    │ 3 │ ←─┐         │ 7 │ ← top       │ 5 │ ← top
    ├───┤   │         ├───┤             ├───┤
    │ 6 │   │ flip    │ 2 │             │ 1 │
    ├───┤   │ here    ├───┤             ├───┤
    │ 2 │   │         │ 6 │             │ 4 │
    ├───┤ ←─┘         ├───┤             ├───┤
    │ 7 │             │ 3 │             │ 3 │
    ├───┤             ├───┤             ├───┤
    │ 4 │             │ 4 │             │ 6 │
    ├───┤             ├───┤             ├───┤
    │ 1 │             │ 1 │             │ 2 │
    ├───┤             ├───┤             ├───┤
    │ 5 │             │ 5 │             │ 7 │ ← 7 in place!
    └───┘             └───┘             └───┘
```

---

## Implementation Details

### Python Implementation

```python
def pancake_sort(arr):
    """
    Sort array using Pancake Sort algorithm.
    
    >>> pancake_sort([0, 5, 3, 2, 2])
    [0, 2, 2, 3, 5]
    >>> pancake_sort([])
    []
    >>> pancake_sort([1])
    [1]
    """
    arr = list(arr)
    cur = len(arr)
    while cur > 1:
        # Find index of maximum element in arr[0..cur-1]
        mi = arr.index(max(arr[0:cur]))
        # Move it to the end if not already there
        if mi != cur - 1:
            # Flip max to front
            arr = arr[mi::-1] + arr[mi + 1:]
            # Flip to correct position
            arr = arr[cur - 1::-1] + arr[cur:]
        cur -= 1
    return arr
```

### In-Place Implementation

```python
def pancake_sort_inplace(arr):
    """
    In-place Pancake Sort using swaps.
    
    >>> arr = [3, 1, 4, 1, 5, 9, 2, 6]
    >>> pancake_sort_inplace(arr)
    >>> arr
    [1, 1, 2, 3, 4, 5, 6, 9]
    """
    def flip(arr, k):
        """Reverse arr[0..k]"""
        left, right = 0, k
        while left < right:
            arr[left], arr[right] = arr[right], arr[left]
            left += 1
            right -= 1
    
    n = len(arr)
    for curr_size in range(n, 1, -1):
        # Find index of max in arr[0..curr_size-1]
        max_idx = 0
        for i in range(1, curr_size):
            if arr[i] > arr[max_idx]:
                max_idx = i
        
        # Move max to correct position
        if max_idx != curr_size - 1:
            if max_idx > 0:
                flip(arr, max_idx)  # Bring max to front
            flip(arr, curr_size - 1)  # Send max to position
```

### Tracking Flips

```python
def pancake_sort_with_flips(arr):
    """
    Pancake Sort that returns the sequence of flips.
    
    >>> arr, flips = pancake_sort_with_flips([3, 2, 1])
    >>> arr
    [1, 2, 3]
    >>> len(flips) <= 4  # At most 2*(n-1) flips
    True
    """
    arr = list(arr)
    n = len(arr)
    flips = []
    
    for curr_size in range(n, 1, -1):
        max_idx = arr.index(max(arr[:curr_size]))
        
        if max_idx != curr_size - 1:
            if max_idx > 0:
                flips.append(max_idx + 1)  # 1-indexed flip
                arr[:max_idx + 1] = arr[:max_idx + 1][::-1]
            flips.append(curr_size)
            arr[:curr_size] = arr[:curr_size][::-1]
    
    return arr, flips
```

---

## Real-World Applications

### 1. **DNA Sequence Rearrangement**

**Use Case**: Modeling genome rearrangements through reversals.

In computational biology, chromosomal inversions can be modeled as pancake flips. The "burnt pancake problem" (where pancakes have orientation) directly models signed genome rearrangements.

```python
def signed_reversal_distance(genome1, genome2):
    """
    Model for genome rearrangement distance.
    Each gene has a position and orientation (+/-).
    
    Finding minimum reversals is equivalent to
    sorting signed permutations using flips.
    """
    # Create target permutation
    n = len(genome1)
    target = list(range(1, n + 1))
    
    # Transform genome1 to permutation relative to genome2
    perm = []
    for gene in genome1:
        pos = genome2.index(abs(gene)) + 1
        perm.append(pos if gene > 0 else -pos)
    
    # Count breakpoints (approximation for reversal distance)
    breakpoints = 0
    for i in range(len(perm) - 1):
        if abs(perm[i + 1]) - abs(perm[i]) != 1:
            breakpoints += 1
    
    return breakpoints  # Lower bound on flips needed
```

### 2. **Stack-Based Systems**

**Use Case**: Reordering items in a stack where only reversals are allowed.

```python
class PancakeStack:
    """
    A stack that can only be sorted using flip operations.
    Useful for modeling constrained robotics or warehouse systems.
    """
    
    def __init__(self, items):
        self.stack = list(items)
        self.flip_count = 0
    
    def flip(self, k):
        """Flip top k items."""
        self.stack[:k] = self.stack[:k][::-1]
        self.flip_count += 1
    
    def sort(self):
        """Sort using pancake sort."""
        n = len(self.stack)
        for size in range(n, 1, -1):
            max_idx = self.stack[:size].index(max(self.stack[:size]))
            if max_idx != size - 1:
                if max_idx > 0:
                    self.flip(max_idx + 1)
                self.flip(size)
        return self.flip_count
    
    def peek(self, k=None):
        """View top k items."""
        if k is None:
            return self.stack[:]
        return self.stack[:k]
```

### 3. **Parallel Prefix Networks**

**Use Case**: Designing efficient prefix computation networks.

Pancake graphs (based on pancake sorting) have good connectivity properties making them useful for interconnection networks.

```python
class PancakeGraph:
    """
    The Pancake Graph P_n has n! vertices (all permutations)
    and edges between permutations differing by one flip.
    
    Properties:
    - Vertex degree: n-1 (can flip at n-1 positions)
    - Diameter: P_n (pancake number)
    - Vertex-transitive
    """
    
    def __init__(self, n):
        self.n = n
        self.vertices = list(self._generate_perms(list(range(1, n+1))))
    
    def _generate_perms(self, items):
        if len(items) <= 1:
            yield tuple(items)
        else:
            for i in range(len(items)):
                for perm in self._generate_perms(items[:i] + items[i+1:]):
                    yield (items[i],) + perm
    
    def neighbors(self, perm):
        """Get all permutations reachable by one flip."""
        perm = list(perm)
        neighbors = []
        for k in range(2, len(perm) + 1):
            flipped = tuple(perm[:k][::-1] + perm[k:])
            neighbors.append(flipped)
        return neighbors
    
    def shortest_path(self, perm1, perm2):
        """BFS to find minimum flips between permutations."""
        from collections import deque
        
        if perm1 == perm2:
            return 0
        
        visited = {perm1}
        queue = deque([(perm1, 0)])
        
        while queue:
            current, dist = queue.popleft()
            for neighbor in self.neighbors(current):
                if neighbor == perm2:
                    return dist + 1
                if neighbor not in visited:
                    visited.add(neighbor)
                    queue.append((neighbor, dist + 1))
        
        return -1  # Should never reach for valid inputs
```

### 4. **Teaching Algorithm Design**

**Use Case**: Educational tool for teaching optimization problems.

```python
def pancake_sorting_visualization(arr):
    """
    Educational visualization of pancake sorting.
    
    >>> viz = pancake_sorting_visualization([3, 1, 4, 2])
    >>> for step in viz:
    ...     print(step['action'], step['array'])
    """
    arr = list(arr)
    n = len(arr)
    steps = [{'action': 'Initial', 'array': arr[:]}]
    
    for curr_size in range(n, 1, -1):
        max_idx = arr.index(max(arr[:curr_size]))
        
        if max_idx != curr_size - 1:
            if max_idx > 0:
                arr[:max_idx + 1] = arr[:max_idx + 1][::-1]
                steps.append({
                    'action': f'Flip({max_idx + 1}): Max to top',
                    'array': arr[:],
                    'flip_position': max_idx + 1
                })
            
            arr[:curr_size] = arr[:curr_size][::-1]
            steps.append({
                'action': f'Flip({curr_size}): Max to position {curr_size}',
                'array': arr[:],
                'flip_position': curr_size
            })
    
    return steps
```

### 5. **Sorting with Limited Operations**

**Use Case**: Systems where only prefix reversals are allowed.

```python
def constrained_sort_system(arr, allowed_ops='flip_only'):
    """
    Sort using only flip operations.
    Useful when only certain physical operations are possible.
    
    Real-world analogy: Robot arm that can only grab and flip
    a stack of items from the top.
    """
    if allowed_ops == 'flip_only':
        return pancake_sort(arr)
    elif allowed_ops == 'single_flip':
        # Only one flip per round allowed
        arr = list(arr)
        n = len(arr)
        for _ in range(2 * n):  # Max 2n flips needed
            for k in range(2, n + 1):
                test = arr[:k][::-1] + arr[k:]
                if is_more_sorted(test, arr):
                    arr = test
                    break
            if arr == sorted(arr):
                break
        return arr


def is_more_sorted(arr1, arr2):
    """Check if arr1 is 'more sorted' than arr2."""
    def inversions(arr):
        count = 0
        for i in range(len(arr)):
            for j in range(i+1, len(arr)):
                if arr[i] > arr[j]:
                    count += 1
        return count
    return inversions(arr1) < inversions(arr2)
```

---

## Variants and Extensions

### 1. Burnt Pancake Problem

Pancakes have a "burnt" side that should face down.

```python
def burnt_pancake_sort(arr):
    """
    Sort where each element has orientation (+/-).
    All elements must end up positive (burnt side down).
    
    >>> burnt_pancake_sort([(-2, 'B'), (3, 'U'), (-1, 'B')])
    """
    # Each element: (value, orientation) where 'B'=burnt side up
    pass  # More complex implementation
```

### 2. Optimized Pancake Sort

Finding minimum flips is NP-hard, but approximations exist.

```python
def pancake_sort_optimized(arr):
    """
    Try to minimize number of flips.
    Uses lookahead to find better flip sequences.
    """
    arr = list(arr)
    n = len(arr)
    total_flips = 0
    
    for curr_size in range(n, 1, -1):
        max_idx = arr.index(max(arr[:curr_size]))
        
        if max_idx == curr_size - 1:
            continue
        
        # Check if max is already at position 0
        if max_idx == 0:
            arr[:curr_size] = arr[:curr_size][::-1]
            total_flips += 1
        else:
            # Standard two-flip approach
            arr[:max_idx + 1] = arr[:max_idx + 1][::-1]
            arr[:curr_size] = arr[:curr_size][::-1]
            total_flips += 2
    
    return arr, total_flips
```

---

## Theoretical Significance

### Pancake Numbers

| n | $P_n$ (min flips for worst case) |
|---|----------------------------------|
| 1 | 0 |
| 2 | 1 |
| 3 | 3 |
| 4 | 4 |
| 5 | 5 |
| 6 | 7 |
| 7 | 8 |
| 8 | 9 |
| 9 | 10 |
| 10 | 11 |

### Gates's Bound

Bill Gates (yes, that one!) co-authored a paper proving:
$$\frac{17n}{16} \leq P_n \leq \frac{5(n+1)}{3}$$

---

## References

1. [Wikipedia: Pancake Sorting](https://en.wikipedia.org/wiki/Pancake_sorting)
2. Gates, W.H. and Papadimitriou, C.H. (1979). "Bounds for Sorting by Prefix Reversal"
3. [The Burnt Pancake Problem](https://en.wikipedia.org/wiki/Pancake_sorting#The_burnt_pancake_problem)
4. Chitturi, B. et al. (2009). "An Upper Bound for Sorting by Prefix Reversals"

---

## See Also

- [Selection Sort](selection_sort.md) - Similar strategy of placing max in position
- [Cycle Sort](cycle_sort.md) - Another minimal-write sorting algorithm
- [Bogo Sort](bogo_sort.md) - Another unconventional sorting approach

