# Binary Insertion Sort

## Overview

| Property | Value |
|----------|-------|
| **Category** | Sorting Algorithm |
| **Type** | Comparison-Based (Insertion Sort Variant) |
| **Data Structure** | Array |
| **Space Complexity** | O(1) |
| **Stable** | Yes |
| **In-Place** | Yes |
| **Adaptive** | Partially |

---

## Mathematical Foundation

### Definition

**Binary Insertion Sort** is an optimized version of Insertion Sort that uses binary search to find the correct position for each element, reducing the number of comparisons while maintaining the same number of shifts.

### Algorithm Principle

For each element $a_i$ (starting from $i=1$):
1. Use **binary search** to find insertion position in sorted portion $[0, i-1]$
2. **Shift** elements to make room
3. **Insert** element at found position

### Binary Search for Insertion

Given sorted array $A[0..i-1]$ and element $x = A[i]$:

Find position $p$ such that:
$$A[p-1] < x \leq A[p]$$ (or $p = 0$ if $x$ is smallest)

Using binary search:
```
low = 0, high = i - 1
while low ≤ high:
    mid = (low + high) / 2
    if x < A[mid]: high = mid - 1
    else: low = mid + 1
return low
```

### Comparison Analysis

**Standard Insertion Sort:**
- Comparisons: $O(n)$ per element (linear search)
- Total comparisons: $O(n^2)$

**Binary Insertion Sort:**
- Comparisons: $O(\log n)$ per element (binary search)
- Total comparisons: $O(n \log n)$

### Why Still O(n²)?

Despite fewer comparisons, the **shift operation** still requires moving elements:
- Shifts per element: $O(n)$ in worst case
- Total shifts: $O(n^2)$

$$T(n) = \underbrace{O(n \log n)}_{\text{comparisons}} + \underbrace{O(n^2)}_{\text{shifts}} = O(n^2)$$

---

## Pseudocode

```
BINARY-INSERTION-SORT(A):
    Input: Array A of n elements
    Output: Sorted array A (in-place)
    
    n ← length(A)
    
    for i ← 1 to n - 1:
        value ← A[i]
        
        // Binary search for insertion position
        low ← 0
        high ← i - 1
        
        while low ≤ high:
            mid ← (low + high) / 2
            if value < A[mid]:
                high ← mid - 1
            else:
                low ← mid + 1
        
        // Shift elements to make room
        for j ← i down to low + 1:
            A[j] ← A[j - 1]
        
        // Insert at correct position
        A[low] ← value
    
    return A
```

### Alternative with Built-in Binary Search

```
BINARY-INSERTION-SORT-BISECT(A):
    Input: Array A of n elements
    Output: Sorted array A (in-place)
    
    for i ← 1 to n - 1:
        value ← A[i]
        pos ← BISECT-LEFT(A[0..i-1], value)
        
        // Shift and insert
        A[pos+1..i] ← A[pos..i-1]  // shift right
        A[pos] ← value
    
    return A
```

---

## Complexity Analysis

### Time Complexity

| Case | Comparisons | Shifts | Total |
|------|-------------|--------|-------|
| **Best** | $O(n \log n)$ | $O(n)$ | $O(n \log n)$ |
| **Average** | $O(n \log n)$ | $O(n^2)$ | $O(n^2)$ |
| **Worst** | $O(n \log n)$ | $O(n^2)$ | $O(n^2)$ |

**Detailed Breakdown:**

| Operation | Complexity per element | Total |
|-----------|----------------------|-------|
| Binary search | $O(\log i)$ | $O(n \log n)$ |
| Shifts | $O(i)$ | $O(n^2)$ |

### Space Complexity

| Component | Space |
|-----------|-------|
| Temporary variable | $O(1)$ |
| **Total** | $O(1)$ |

### Comparison: Standard vs Binary Insertion Sort

| Metric | Standard Insertion | Binary Insertion |
|--------|-------------------|------------------|
| Comparisons | $O(n^2)$ | $O(n \log n)$ |
| Shifts | $O(n^2)$ | $O(n^2)$ |
| Total | $O(n^2)$ | $O(n^2)$ |
| Best case | $O(n)$ | $O(n \log n)$ |

---

## Visual Representation

### Algorithm Flow

```mermaid
flowchart TD
    A[Start] --> B[i = 1]
    B --> C{i < n?}
    C -->|Yes| D[value = A[i]]
    D --> E[Binary search in A[0..i-1]]
    E --> F[Find insertion position 'pos']
    F --> G[Shift A[pos..i-1] right by 1]
    G --> H[A[pos] = value]
    H --> I[i++]
    I --> C
    C -->|No| J[Return sorted A]
```

### Sorting Example

```
Input: [4, 1, 3, 2]

i = 1, value = 1:
  Sorted portion: [4]
  Binary search: 1 < 4, pos = 0
  Shift: [4] → [_, 4]
  Insert: [1, 4]
  Array: [1, 4, 3, 2]

i = 2, value = 3:
  Sorted portion: [1, 4]
  Binary search: 
    mid=0, 3 > 1 → low=1
    mid=1, 3 < 4 → high=0
    low=1, high=0 → pos=1
  Shift: [1, 4] → [1, _, 4]
  Insert: [1, 3, 4]
  Array: [1, 3, 4, 2]

i = 3, value = 2:
  Sorted portion: [1, 3, 4]
  Binary search:
    mid=1, 2 < 3 → high=0
    mid=0, 2 > 1 → low=1
    low=1, high=0 → pos=1
  Shift: [1, 3, 4] → [1, _, 3, 4]
  Insert: [1, 2, 3, 4]
  Array: [1, 2, 3, 4]

Output: [1, 2, 3, 4]
```

### Binary Search Visualization

```
Finding position for value=5 in [1, 3, 7, 9]:

Step 1: low=0, high=3, mid=1
        [1, 3, 7, 9]
            ↑
        5 > 3 → low = 2

Step 2: low=2, high=3, mid=2
        [1, 3, 7, 9]
               ↑
        5 < 7 → high = 1

Step 3: low=2, high=1 → STOP
        Position = low = 2

Result: Insert 5 at position 2
        [1, 3, _, 7, 9] → [1, 3, 5, 7, 9]
```

---

## Implementation Details

### Python Implementation

```python
def binary_insertion_sort(collection: list) -> list:
    """
    Sorts a list using the binary insertion sort algorithm.

    :param collection: A mutable ordered collection with comparable items.
    :return: The same collection ordered in ascending order.

    Examples:
    >>> binary_insertion_sort([0, 4, 1234, 4, 1])
    [0, 1, 4, 4, 1234]
    >>> binary_insertion_sort([]) == sorted([])
    True
    >>> binary_insertion_sort([-1, -2, -3]) == sorted([-1, -2, -3])
    True
    >>> lst = ['d', 'a', 'b', 'e', 'c']
    >>> binary_insertion_sort(lst) == sorted(lst)
    True
    """
    n = len(collection)
    for i in range(1, n):
        value_to_insert = collection[i]
        low = 0
        high = i - 1

        # Binary search for insertion position
        while low <= high:
            mid = (low + high) // 2
            if value_to_insert < collection[mid]:
                high = mid - 1
            else:
                low = mid + 1
        
        # Shift elements and insert
        for j in range(i, low, -1):
            collection[j] = collection[j - 1]
        collection[low] = value_to_insert
    
    return collection
```

### Using bisect Module

```python
from bisect import bisect_left


def binary_insertion_sort_bisect(arr: list) -> list:
    """
    Binary insertion sort using Python's bisect module.
    
    >>> binary_insertion_sort_bisect([5, 2, 4, 1, 3])
    [1, 2, 3, 4, 5]
    """
    for i in range(1, len(arr)):
        value = arr[i]
        pos = bisect_left(arr, value, 0, i)
        
        # Shift and insert
        for j in range(i, pos, -1):
            arr[j] = arr[j - 1]
        arr[pos] = value
    
    return arr
```

### With Slicing (Less Efficient but Cleaner)

```python
def binary_insertion_sort_slice(arr: list) -> list:
    """
    Binary insertion sort using array slicing for insertion.
    Note: This creates copies, so not truly O(1) space.
    
    >>> binary_insertion_sort_slice([5, 2, 4, 1, 3])
    [1, 2, 3, 4, 5]
    """
    from bisect import bisect_left
    
    result = []
    for item in arr:
        pos = bisect_left(result, item)
        result.insert(pos, item)  # O(n) operation
    
    return result
```

### Recursive Version

```python
def binary_insertion_sort_recursive(arr: list, n: int = None) -> list:
    """
    Recursive binary insertion sort.
    
    >>> binary_insertion_sort_recursive([5, 2, 4, 1, 3])
    [1, 2, 3, 4, 5]
    """
    if n is None:
        n = len(arr)
    
    if n <= 1:
        return arr
    
    # Sort first n-1 elements
    binary_insertion_sort_recursive(arr, n - 1)
    
    # Insert nth element using binary search
    value = arr[n - 1]
    low, high = 0, n - 2
    
    while low <= high:
        mid = (low + high) // 2
        if value < arr[mid]:
            high = mid - 1
        else:
            low = mid + 1
    
    # Shift and insert
    for j in range(n - 1, low, -1):
        arr[j] = arr[j - 1]
    arr[low] = value
    
    return arr
```

---

## Real-World Applications

### 1. **Timsort Component**

**Use Case**: Python's Timsort uses binary insertion sort for small runs.

```python
def timsort_binary_insertion(arr: list, left: int, right: int) -> None:
    """
    Binary insertion sort for Timsort small runs.
    This is how Python's sorted() handles small segments.
    
    >>> arr = [5, 2, 4, 1, 3]
    >>> timsort_binary_insertion(arr, 0, 4)
    >>> arr
    [1, 2, 3, 4, 5]
    """
    from bisect import bisect_left
    
    for i in range(left + 1, right + 1):
        value = arr[i]
        pos = bisect_left(arr, value, left, i)
        
        # Shift elements
        for j in range(i, pos, -1):
            arr[j] = arr[j - 1]
        arr[pos] = value
```

### 2. **Sorting with Expensive Comparisons**

**Use Case**: When comparisons are expensive (e.g., string comparisons).

```python
def sort_strings(strings: list[str]) -> list[str]:
    """
    Sort strings using binary insertion sort.
    Reduces expensive string comparisons.
    
    >>> sort_strings(['banana', 'apple', 'cherry'])
    ['apple', 'banana', 'cherry']
    """
    comparison_count = 0
    
    def compare(s1, s2):
        nonlocal comparison_count
        comparison_count += 1
        return s1 < s2
    
    n = len(strings)
    for i in range(1, n):
        value = strings[i]
        low, high = 0, i - 1
        
        while low <= high:
            mid = (low + high) // 2
            if compare(value, strings[mid]):
                high = mid - 1
            else:
                low = mid + 1
        
        for j in range(i, low, -1):
            strings[j] = strings[j - 1]
        strings[low] = value
    
    print(f"Comparisons: {comparison_count}")
    return strings
```

### 3. **Online Sorting**

**Use Case**: Maintaining sorted order as elements arrive.

```python
class OnlineSorter:
    """
    Maintain sorted list as elements arrive one at a time.
    Uses binary search for efficient insertion position finding.
    """
    
    def __init__(self):
        self.sorted_list = []
    
    def insert(self, value):
        """
        Insert value maintaining sorted order.
        O(log n) to find position, O(n) to insert.
        
        >>> sorter = OnlineSorter()
        >>> sorter.insert(3)
        >>> sorter.insert(1)
        >>> sorter.insert(2)
        >>> sorter.get_sorted()
        [1, 2, 3]
        """
        from bisect import bisect_left
        pos = bisect_left(self.sorted_list, value)
        self.sorted_list.insert(pos, value)
    
    def get_sorted(self):
        return self.sorted_list
    
    def get_rank(self, value):
        """Get rank of value (how many elements are smaller)."""
        from bisect import bisect_left
        return bisect_left(self.sorted_list, value)
```

### 4. **Priority-Based Task Scheduling**

**Use Case**: Inserting tasks in priority order.

```python
class PriorityTaskQueue:
    """
    Task queue sorted by priority using binary insertion.
    """
    
    def __init__(self):
        self.tasks = []  # [(priority, task_id, task)]
    
    def add_task(self, priority: int, task_id: int, task: callable):
        """
        Add task maintaining priority order.
        Lower priority number = higher priority.
        """
        new_task = (priority, task_id, task)
        
        # Binary search for position
        low, high = 0, len(self.tasks) - 1
        while low <= high:
            mid = (low + high) // 2
            if priority < self.tasks[mid][0]:
                high = mid - 1
            else:
                low = mid + 1
        
        self.tasks.insert(low, new_task)
    
    def get_next_task(self):
        """Get highest priority task."""
        if self.tasks:
            return self.tasks.pop(0)[2]
        return None
    
    def peek(self, n=5):
        """View top n tasks."""
        return [(p, tid) for p, tid, _ in self.tasks[:n]]
```

### 5. **Leaderboard Maintenance**

**Use Case**: Keeping scores sorted in a leaderboard.

```python
class Leaderboard:
    """
    Maintain sorted leaderboard using binary insertion sort.
    """
    
    def __init__(self, max_entries=100):
        self.entries = []  # [(score, player_name)]
        self.max_entries = max_entries
    
    def submit_score(self, player: str, score: int):
        """
        Submit score, maintaining descending order.
        
        >>> lb = Leaderboard()
        >>> lb.submit_score("Alice", 100)
        >>> lb.submit_score("Bob", 150)
        >>> lb.submit_score("Charlie", 125)
        >>> lb.get_top(3)
        [('Bob', 150), ('Charlie', 125), ('Alice', 100)]
        """
        new_entry = (score, player)
        
        # Binary search (for descending order)
        low, high = 0, len(self.entries) - 1
        while low <= high:
            mid = (low + high) // 2
            if score > self.entries[mid][0]:
                high = mid - 1
            else:
                low = mid + 1
        
        self.entries.insert(low, new_entry)
        
        # Trim if exceeds max
        if len(self.entries) > self.max_entries:
            self.entries = self.entries[:self.max_entries]
    
    def get_top(self, n: int):
        """Get top n entries."""
        return [(name, score) for score, name in self.entries[:n]]
    
    def get_rank(self, player: str):
        """Get player's rank (1-indexed)."""
        for i, (_, name) in enumerate(self.entries):
            if name == player:
                return i + 1
        return None
```

---

## Comparison with Other Variants

| Algorithm | Comparisons | Shifts | Total | Best For |
|-----------|-------------|--------|-------|----------|
| Standard Insertion | $O(n^2)$ | $O(n^2)$ | $O(n^2)$ | Nearly sorted |
| Binary Insertion | $O(n \log n)$ | $O(n^2)$ | $O(n^2)$ | Expensive comparisons |
| Shell Sort | $O(n^{1.3})$ | $O(n^{1.3})$ | $O(n^{1.3})$ | Medium arrays |

---

## When to Use Binary Insertion Sort

### ✅ Use When

1. **Comparisons are expensive** (strings, objects)
2. **Small to medium arrays** ($n < 50$)
3. **As part of hybrid sort** (like Timsort)
4. **Online insertion** needed
5. **Nearly sorted data** with few inversions

### ❌ Avoid When

1. **Large arrays** - use $O(n \log n)$ algorithms
2. **Random data** - shifts still dominate
3. **Memory allocation is cheap** - merge sort may be better

---

## References

1. [Wikipedia: Binary Insertion Sort](https://en.wikipedia.org/wiki/Insertion_sort#Variants)
2. [Python's Timsort](https://en.wikipedia.org/wiki/Timsort)
3. Sedgewick, R. "Algorithms in C++"

---

## See Also

- [Insertion Sort](insertion_sort.md) - Standard version
- [Shell Sort](shell_sort.md) - Gap-based insertion sort
- [Tim Sort](tim_sort.md) - Uses binary insertion for small runs

