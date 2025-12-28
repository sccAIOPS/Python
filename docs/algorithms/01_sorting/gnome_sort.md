# Gnome Sort

## Overview

| Property | Value |
|----------|-------|
| **Category** | Sorting Algorithm |
| **Type** | Comparison-Based (Exchange Sort) |
| **Data Structure** | Array |
| **Space Complexity** | O(1) |
| **Stable** | Yes |
| **In-Place** | Yes |
| **Adaptive** | Yes |

**Also Known As**: Stupid Sort, Garden Gnome Sort

---

## Mathematical Foundation

### Definition

Gnome Sort is a sorting algorithm similar to insertion sort, except that moving an element to its proper place is accomplished by a series of swaps, similar to bubble sort. The algorithm always finds the first place where two adjacent elements are in the wrong order and swaps them.

### Origin

The algorithm was described by **Hamid Sarbazi-Azad** in 2000 and was called "Stupid Sort". It was later described by **Dick Grune** and named "Gnome Sort" because of its similarity to how a garden gnome sorts a line of flower pots.

### Algorithm Intuition (Garden Gnome Metaphor)

Imagine a garden gnome sorting flower pots:
1. Start at pot 1
2. Look at pot to the left:
   - If no pot to left, or left pot is smaller, move one step right
   - If left pot is larger, swap them and move one step left
3. Repeat until reaching the end of the line

### Formal Description

Given an array $A = [a_0, a_1, ..., a_{n-1}]$:

$$i \leftarrow 1$$

**While** $i < n$:
$$\text{if } a_{i-1} \leq a_i: \quad i \leftarrow i + 1$$
$$\text{else: swap}(a_{i-1}, a_i), \quad i \leftarrow \max(i-1, 1)$$

### Inversion Analysis

Each swap eliminates exactly one inversion. The total number of swaps equals the number of inversions.

**For random permutations:**
$$E[\text{inversions}] = \frac{n(n-1)}{4}$$

**Therefore:**
$$E[\text{swaps}] = \frac{n(n-1)}{4} = O(n^2)$$

### Comparison Count

**Forward moves**: $n - 1$ (reaching the end)
**Backward moves**: Number of inversions

$$\text{Total comparisons} = (n-1) + \text{inversions} = O(n^2)$$

---

## Pseudocode

### Basic Version

```
GNOME-SORT(A):
    Input: Array A of n elements
    Output: Sorted array A (in-place)
    
    if n <= 1:
        return A
    
    i ← 1
    
    while i < n:
        if A[i - 1] <= A[i]:
            // Elements in order, move forward
            i ← i + 1
        else:
            // Elements out of order, swap and move back
            swap(A[i - 1], A[i])
            i ← i - 1
            
            // Don't go before start
            if i == 0:
                i ← 1
    
    return A
```

### Optimized Version (with position memory)

```
GNOME-SORT-OPTIMIZED(A):
    Input: Array A of n elements
    Output: Sorted array A (in-place)
    
    i ← 1
    j ← 2  // Remember where to continue
    
    while i < n:
        if A[i - 1] <= A[i]:
            // Move to remembered position
            i ← j
            j ← j + 1
        else:
            swap(A[i - 1], A[i])
            i ← i - 1
            if i == 0:
                i ← j
                j ← j + 1
    
    return A
```

---

## Complexity Analysis

### Time Complexity

| Case | Complexity | Condition |
|------|------------|-----------|
| **Best** | $O(n)$ | Already sorted |
| **Average** | $O(n^2)$ | Random arrangement |
| **Worst** | $O(n^2)$ | Reverse sorted |

### Detailed Analysis

**Best Case $O(n)$:**
- Array is already sorted
- Single pass forward: $n - 1$ comparisons
- No swaps needed

**Worst Case $O(n^2)$:**
- Reverse sorted array
- Maximum inversions: $\frac{n(n-1)}{2}$
- Total operations: $n + \frac{n(n-1)}{2} = O(n^2)$

**Average Case $O(n^2)$:**
- Expected inversions: $\frac{n(n-1)}{4}$
- Total operations: $O(n^2)$

### Space Complexity

| Type | Complexity |
|------|------------|
| Auxiliary | $O(1)$ |
| Total | $O(n)$ (input only) |

---

## Visual Representation

### Algorithm Flow

```mermaid
flowchart TD
    A[Start: i = 1] --> B{i < n?}
    B -->|No| H[Done: Array sorted]
    B -->|Yes| C{A[i-1] <= A[i]?}
    C -->|Yes| D[i = i + 1]
    C -->|No| E[Swap A[i-1], A[i]]
    E --> F[i = i - 1]
    F --> G{i == 0?}
    G -->|Yes| I[i = 1]
    G -->|No| B
    I --> B
    D --> B
```

### Visual Example

```
Initial: [5, 3, 1, 4, 2]
         i=1
         ↓
Step 1:  [5, 3, 1, 4, 2]  5 > 3, swap
         [3, 5, 1, 4, 2]  i = 0 → i = 1
              ↑

Step 2:  [3, 5, 1, 4, 2]  3 <= 5, move right
              i=2
              ↓
Step 3:  [3, 5, 1, 4, 2]  5 > 1, swap
         [3, 1, 5, 4, 2]  i = 1
            ↑

Step 4:  [3, 1, 5, 4, 2]  3 > 1, swap
         [1, 3, 5, 4, 2]  i = 0 → i = 1
            ↑

Step 5:  [1, 3, 5, 4, 2]  1 <= 3, move right
                 i=3
                 ↓
Step 6:  [1, 3, 5, 4, 2]  5 > 4, swap
         [1, 3, 4, 5, 2]  i = 2
              ↑

Step 7:  [1, 3, 4, 5, 2]  3 <= 4, move right
                    i=4
                    ↓
Step 8:  [1, 3, 4, 5, 2]  5 > 2, swap
         [1, 3, 4, 2, 5]  i = 3
               ↑

Step 9:  [1, 3, 4, 2, 5]  4 > 2, swap
         [1, 3, 2, 4, 5]  i = 2
            ↑

Step 10: [1, 3, 2, 4, 5]  3 > 2, swap
         [1, 2, 3, 4, 5]  i = 1
         ↑

Step 11: [1, 2, 3, 4, 5]  1 <= 2, move to end
                       ✓

Final:   [1, 2, 3, 4, 5]
```

### Gnome Metaphor Visualization

```
Garden Gnome sorting flower pots by height:

🌼1 🌼3 🌼5 🌼4 🌼2    Gnome at position 3
         🧙
         
"Pot 5 > Pot 4, swap and step back"

🌼1 🌼3 🌼4 🌼5 🌼2    Gnome at position 2
      🧙
      
"Pot 3 <= Pot 4, step forward"

🌼1 🌼3 🌼4 🌼5 🌼2    Gnome at position 4
            🧙

"Continue until end reached with all in order"

🌼1 🌼2 🌼3 🌼4 🌼5    Done!
               🧙
```

---

## Implementation Details

### Python Implementation

```python
def gnome_sort(lst: list) -> list:
    """
    Pure implementation of the gnome sort algorithm in Python.
    
    Examples:
    >>> gnome_sort([0, 5, 3, 2, 2])
    [0, 2, 2, 3, 5]
    >>> gnome_sort([])
    []
    >>> gnome_sort([-2, -5, -45])
    [-45, -5, -2]
    """
    if len(lst) <= 1:
        return lst
    
    i = 1
    
    while i < len(lst):
        if lst[i - 1] <= lst[i]:
            i += 1
        else:
            lst[i - 1], lst[i] = lst[i], lst[i - 1]
            i -= 1
            if i == 0:
                i = 1
    
    return lst
```

### Optimized Version (Reduces Comparisons)

```python
def gnome_sort_optimized(lst: list) -> list:
    """
    Optimized gnome sort that remembers position.
    Reduces redundant comparisons after swapping.
    """
    if len(lst) <= 1:
        return lst
    
    i = 1
    j = 2  # Position to return to
    
    while i < len(lst):
        if lst[i - 1] <= lst[i]:
            # Jump to remembered position
            i = j
            j += 1
        else:
            lst[i - 1], lst[i] = lst[i], lst[i - 1]
            i -= 1
            if i == 0:
                i = j
                j += 1
    
    return lst
```

### Edge Cases

| Case | Handling |
|------|----------|
| Empty array | Return empty |
| Single element | Return as-is |
| Already sorted | Single pass, O(n) |
| Reverse sorted | Maximum comparisons |
| All equal | Single pass, O(n) |
| Two elements | One comparison (+ swap if needed) |

---

## Real-World Applications

### 1. **Educational Purposes**

**Use Case**: Teaching sorting algorithms due to simplicity.

```python
class SortingTutor:
    """
    Educational sorting demonstrator.
    Gnome sort is perfect for teaching because:
    - Extremely simple algorithm
    - Easy to trace step-by-step
    - Shows insertion behavior with exchange mechanism
    """
    
    def __init__(self, data):
        self.data = data
        self.steps = []
    
    def demonstrate_sort(self):
        """Show each step of gnome sort."""
        lst = self.data.copy()
        i = 1
        step = 0
        
        while i < len(lst):
            step += 1
            if lst[i - 1] <= lst[i]:
                self.steps.append({
                    'step': step,
                    'action': 'compare',
                    'position': i,
                    'result': 'move_forward',
                    'array': lst.copy()
                })
                i += 1
            else:
                self.steps.append({
                    'step': step,
                    'action': 'swap',
                    'position': i,
                    'swapped': (lst[i-1], lst[i]),
                    'array': lst.copy()
                })
                lst[i - 1], lst[i] = lst[i], lst[i - 1]
                i = max(i - 1, 1)
        
        return self.steps
```

### 2. **Small Dataset Sorting**

**Use Case**: Sorting very small arrays where simplicity matters.

```python
# Embedded system with very limited resources
def sort_sensor_readings(readings: list, max_readings=5):
    """
    Sort small number of sensor readings.
    Gnome sort is efficient for n < 10.
    
    Memory usage: Just a single index variable beyond the array.
    """
    if len(readings) <= 1:
        return readings
    
    i = 1
    while i < len(readings):
        if readings[i - 1] <= readings[i]:
            i += 1
        else:
            readings[i - 1], readings[i] = readings[i], readings[i - 1]
            i = max(i - 1, 1)
    
    return readings
```

### 3. **Interactive Sorting Visualization**

**Use Case**: Creating sorting visualizations for web or mobile apps.

```python
class GnomeSortVisualizer:
    """
    Gnome sort visualization - perfect because:
    - Single index makes animation simple
    - Clear forward/backward movement
    - Easy to show "gnome walking" metaphor
    """
    
    def __init__(self, canvas, data):
        self.canvas = canvas
        self.data = data.copy()
        self.gnome_pos = 1
    
    def step(self):
        """Execute one step and return state for rendering."""
        if self.gnome_pos >= len(self.data):
            return {'done': True, 'array': self.data}
        
        state = {
            'done': False,
            'position': self.gnome_pos,
            'comparing': (self.gnome_pos - 1, self.gnome_pos),
            'array': self.data.copy()
        }
        
        if self.data[self.gnome_pos - 1] <= self.data[self.gnome_pos]:
            state['action'] = 'forward'
            self.gnome_pos += 1
        else:
            state['action'] = 'swap_and_back'
            self.data[self.gnome_pos - 1], self.data[self.gnome_pos] = \
                self.data[self.gnome_pos], self.data[self.gnome_pos - 1]
            self.gnome_pos = max(self.gnome_pos - 1, 1)
        
        return state
```

### 4. **Online/Streaming Sort**

**Use Case**: Sorting data that arrives incrementally.

```python
class OnlineGnomeSort:
    """
    Online sorting where elements arrive one at a time.
    After each insert, the array remains sorted.
    """
    
    def __init__(self):
        self.data = []
    
    def insert(self, value):
        """Insert value and maintain sorted order."""
        self.data.append(value)
        
        # Gnome sort behavior for single insert
        i = len(self.data) - 1
        while i > 0 and self.data[i - 1] > self.data[i]:
            self.data[i - 1], self.data[i] = self.data[i], self.data[i - 1]
            i -= 1
        
        return self.data
    
    def get_sorted(self):
        return self.data.copy()

# Usage
sorter = OnlineGnomeSort()
for value in stream:
    sorted_so_far = sorter.insert(value)
```

---

## When to Use Gnome Sort

### ✅ Ideal Scenarios

1. **Educational purposes** - Simplest sorting algorithm
2. **Very small arrays** ($n < 10$)
3. **Code golf / minimal code** - Can be written in few lines
4. **Single-pass sorting** with incremental data
5. **Visualization projects** - Easy to animate

### ❌ Avoid When

1. **Any production code** - Too slow
2. **Arrays larger than ~10 elements**
3. **Performance matters at all**

---

## Comparison with Related Algorithms

| Algorithm | Code Complexity | Best | Average | Worst | Moves |
|-----------|-----------------|------|---------|-------|-------|
| Gnome Sort | Very Simple | $O(n)$ | $O(n^2)$ | $O(n^2)$ | $O(n^2)$ |
| Insertion Sort | Simple | $O(n)$ | $O(n^2)$ | $O(n^2)$ | $O(n^2)$ |
| Bubble Sort | Simple | $O(n)$ | $O(n^2)$ | $O(n^2)$ | $O(n^2)$ |

**Key Difference from Insertion Sort:**
- Insertion sort uses a binary search + shift approach
- Gnome sort uses swap + move back approach
- Same number of comparisons, but gnome sort has more swaps

---

## Code Simplicity

Gnome sort can be written extremely concisely:

```python
# One-liner (excluding function definition)
def gnome(a):
    i = 0
    while i < len(a):
        if i == 0 or a[i-1] <= a[i]: i += 1
        else: a[i-1], a[i], i = a[i], a[i-1], i-1
    return a
```

---

## References

1. Sarbazi-Azad, H. (2000). "Stupid Sort: A new sorting algorithm"
2. Grune, D. "Gnome Sort - The Simplest Sort Algorithm"
3. [Wikipedia: Gnome Sort](https://en.wikipedia.org/wiki/Gnome_sort)

---

## See Also

- [Insertion Sort](insertion_sort.md) - More efficient variant of same concept
- [Bubble Sort](bubble_sort.md) - Similar exchange-based sort
- [Cocktail Shaker Sort](cocktail_shaker_sort.md) - Bidirectional exchange sort

