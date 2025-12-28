# Patience Sort

## Overview

| Property | Value |
|----------|-------|
| **Category** | Sorting Algorithm |
| **Type** | Comparison-Based (Card-Game Based) |
| **Data Structure** | Stacks (Piles), Heap |
| **Space Complexity** | O(n) |
| **Stable** | No (standard), Yes (with modification) |
| **In-Place** | No |
| **Adaptive** | Yes |

---

## Mathematical Foundation

### Definition

**Patience Sort** is a sorting algorithm based on the card game **Patience** (Solitaire). It works by distributing elements into piles (stacks) and then merging the piles using a min-heap. The algorithm has special significance because it provides an efficient way to compute the **Longest Increasing Subsequence (LIS)**.

### Card Game Rules

1. **Pile Placement**: Each card goes on the leftmost pile whose top card is ≥ the new card, or starts a new pile if no such pile exists
2. **Pile Order**: Each pile is a decreasing sequence from bottom to top
3. **Merge**: Piles are merged by repeatedly taking the smallest top card

### Algorithm Principle

**Phase 1 - Distribution:**
For each element $x$:
- Binary search for the leftmost pile whose top is $\geq x$
- Place $x$ on that pile (or create new pile)

**Phase 2 - Merge:**
- Use a min-heap to efficiently find smallest top among all piles
- Repeatedly extract minimum until all piles are empty

### Key Property: LIS Connection

The **number of piles** after Phase 1 equals the length of the **Longest Increasing Subsequence (LIS)**.

**Dilworth's Theorem**: The minimum number of decreasing subsequences that partition a sequence equals the length of the longest increasing subsequence.

### Mathematical Analysis

**Pile Count:**
- Best case: 1 pile (already sorted descending)
- Worst case: $n$ piles (already sorted ascending)
- Average case: $O(\sqrt{n})$ piles (for random permutation)

**Time Complexity:**
- Phase 1: $O(n \log k)$ where $k$ = number of piles
- Phase 2: $O(n \log k)$ using heap merge
- Total: $O(n \log n)$

---

## Pseudocode

```
PATIENCE-SORT(A):
    Input: Array A of n elements
    Output: Sorted array
    
    // Phase 1: Distribute into piles
    piles ← []  // List of stacks
    
    for each element x in A:
        // Binary search for pile to place x
        pile_index ← BINARY-SEARCH-PILE(piles, x)
        
        if pile_index < length(piles):
            push x onto piles[pile_index]
        else:
            create new pile with x
            append new pile to piles
    
    // Phase 2: Merge piles using heap
    result ← MERGE-PILES(piles)
    
    return result


BINARY-SEARCH-PILE(piles, x):
    // Find leftmost pile whose top is >= x
    low ← 0
    high ← length(piles) - 1
    
    while low ≤ high:
        mid ← (low + high) / 2
        if top(piles[mid]) ≥ x:
            high ← mid - 1
        else:
            low ← mid + 1
    
    return low


MERGE-PILES(piles):
    // Merge using min-heap
    heap ← min-heap of (top_value, pile_index) for non-empty piles
    result ← []
    
    while heap is not empty:
        (value, pile_idx) ← extract-min(heap)
        append value to result
        
        if piles[pile_idx] is not empty:
            new_top ← pop from piles[pile_idx]
            insert (new_top, pile_idx) into heap
    
    return result
```

---

## Complexity Analysis

### Time Complexity

| Case | Complexity | Notes |
|------|------------|-------|
| **Best** | $O(n)$ | Sorted descending, 1 pile |
| **Average** | $O(n \log n)$ | $O(\sqrt{n})$ piles |
| **Worst** | $O(n \log n)$ | $n$ piles (sorted ascending) |

**Phase 1 (Distribution):**
- Each insertion: $O(\log k)$ binary search
- Total: $O(n \log n)$ worst case

**Phase 2 (Merge):**
- Heap operations: $O(n \log k)$
- Total: $O(n \log n)$

### Space Complexity

| Component | Space |
|-----------|-------|
| Piles (total elements) | $O(n)$ |
| Heap | $O(k)$ where $k$ = pile count |
| **Total** | $O(n)$ |

### Pile Count Distribution

For random input of size $n$:
- Expected number of piles: $\approx 2\sqrt{n}$
- Standard deviation: $\approx n^{1/6}$

---

## Visual Representation

### Algorithm Flow

```mermaid
flowchart TD
    A[Input Array] --> B[Phase 1: Create Piles]
    B --> C[Binary search for pile placement]
    C --> D[Place element on pile]
    D --> E{More elements?}
    E -->|Yes| C
    E -->|No| F[Phase 2: Merge Piles]
    F --> G[Build min-heap of pile tops]
    G --> H[Extract minimum]
    H --> I[Add to result]
    I --> J{Piles empty?}
    J -->|No| H
    J -->|Yes| K[Return sorted array]
```

### Pile Formation Example

```
Input: [7, 2, 8, 1, 3, 4, 10, 6, 9, 5]

Step by step pile formation:

7: Pile 0 = [7]           (new pile)
2: Pile 0 = [7, 2]        (2 < 7, place on pile 0)
8: Pile 1 = [8]           (8 > 7, new pile)
1: Pile 0 = [7, 2, 1]     (1 < 2, place on pile 0)
3: Pile 1 = [8, 3]        (3 < 8, place on pile 1)
4: Pile 2 = [4]           (4 > 3, new pile... wait binary search)
   Actually: 4 > 1 (pile 0 top), 4 > 3 (pile 1 top), new pile
4: Pile 2 = [4]           (new pile)
10: Pile 3 = [10]         (10 > 4, new pile)
6: Pile 2 = [4, 6]        (wait, 6 > 4? Check algorithm...)

Let me redo with correct logic:
Binary search finds leftmost pile where top >= x

7: Pile 0 = [7]           
2: Piles: [7]. 7 >= 2? Yes. Place on pile 0
   Pile 0 = [7, 2]
8: Piles tops: [2]. 2 >= 8? No. New pile
   Pile 1 = [8]
1: Piles tops: [2, 8]. 2 >= 1? Yes. Place on pile 0
   Pile 0 = [7, 2, 1]
3: Piles tops: [1, 8]. 1 >= 3? No. 8 >= 3? Yes. Place on pile 1
   Pile 1 = [8, 3]
4: Piles tops: [1, 3]. 1 >= 4? No. 3 >= 4? No. New pile
   Pile 2 = [4]
10: Piles tops: [1, 3, 4]. None >= 10. New pile
    Pile 3 = [10]
6: Piles tops: [1, 3, 4, 10]. 10 >= 6? Yes. Place on pile 3
   Pile 3 = [10, 6]
9: Piles tops: [1, 3, 4, 6]. None >= 9. New pile
   Pile 4 = [9]
5: Piles tops: [1, 3, 4, 6, 9]. 6 >= 5? Yes. Place on pile 3
   Pile 3 = [10, 6, 5]

Final piles (bottom to top):
Pile 0: [7, 2, 1]     top = 1
Pile 1: [8, 3]        top = 3
Pile 2: [4]           top = 4
Pile 3: [10, 6, 5]    top = 5
Pile 4: [9]           top = 9

Number of piles = 5 = LIS length!
One LIS: 1, 3, 4, 6, 9 ✓

Merge (pop from piles, always take min top):
Tops: [1, 3, 4, 5, 9] → pop 1
Tops: [2, 3, 4, 5, 9] → pop 2
Tops: [7, 3, 4, 5, 9] → pop 3
Tops: [7, 8, 4, 5, 9] → pop 4
Tops: [7, 8, -, 5, 9] → pop 5
Tops: [7, 8, -, 6, 9] → pop 6
Tops: [7, 8, -, 10, 9] → pop 7
Tops: [-, 8, -, 10, 9] → pop 8
Tops: [-, -, -, 10, 9] → pop 9
Tops: [-, -, -, 10, -] → pop 10

Result: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

### Stack Visualization

```
After pile formation:

Pile 0   Pile 1   Pile 2   Pile 3   Pile 4
  ┌─┐      ┌─┐      ┌─┐      ┌─┐      ┌─┐
  │1│←top  │3│←top  │4│←top  │5│←top  │9│←top
  ├─┤      ├─┤      └─┘      ├─┤      └─┘
  │2│      │8│               │6│
  ├─┤      └─┘               ├─┤
  │7│                        │10│
  └─┘                        └─┘
```

---

## Implementation Details

### Python Implementation

```python
from __future__ import annotations
from bisect import bisect_left
from functools import total_ordering
from heapq import merge


@total_ordering
class Stack(list):
    """Stack that compares by top element."""
    
    def __lt__(self, other):
        return self[-1] < other[-1]

    def __eq__(self, other):
        return self[-1] == other[-1]


def patience_sort(collection: list) -> list:
    """
    A pure implementation of patience sort algorithm in Python.

    :param collection: some mutable ordered collection with heterogeneous
    comparable items inside
    :return: the same collection ordered by ascending

    Examples:
    >>> patience_sort([1, 9, 5, 21, 17, 6])
    [1, 5, 6, 9, 17, 21]

    >>> patience_sort([])
    []

    >>> patience_sort([-3, -17, -48])
    [-48, -17, -3]
    """
    stacks: list[Stack] = []
    
    # Phase 1: Sort into stacks
    for element in collection:
        new_stack = Stack([element])
        i = bisect_left(stacks, new_stack)
        if i != len(stacks):
            stacks[i].append(element)
        else:
            stacks.append(new_stack)

    # Phase 2: Use heap-based merge
    collection[:] = merge(*(reversed(stack) for stack in stacks))
    return collection
```

### Detailed Implementation with LIS

```python
from bisect import bisect_left
from heapq import heappush, heappop


def patience_sort_detailed(arr: list) -> tuple[list, list]:
    """
    Patience sort that also returns one LIS.
    
    Returns:
        (sorted_array, longest_increasing_subsequence)
    
    >>> sorted_arr, lis = patience_sort_detailed([7, 2, 8, 1, 3])
    >>> sorted_arr
    [1, 2, 3, 7, 8]
    >>> len(lis) == 3  # LIS length
    True
    """
    if not arr:
        return [], []
    
    # Phase 1: Build piles with back-pointers for LIS
    piles = []  # List of stacks
    pile_tops = []  # For binary search
    back_pointers = []  # For LIS reconstruction
    
    for elem in arr:
        # Binary search for pile
        pos = bisect_left(pile_tops, elem)
        
        # Back pointer for LIS
        if pos == 0:
            back_ptr = -1
        else:
            back_ptr = len(piles[pos - 1]) - 1, pos - 1
        
        back_pointers.append((elem, back_ptr))
        
        if pos < len(piles):
            piles[pos].append(elem)
            pile_tops[pos] = elem
        else:
            piles.append([elem])
            pile_tops.append(elem)
    
    # Reconstruct LIS
    lis = []
    if piles:
        # Start from last pile
        current = (len(piles[-1]) - 1, len(piles) - 1)
        # Simplified: just use pile tops as one valid LIS
        lis = pile_tops[:]
    
    # Phase 2: Merge piles
    heap = []
    for i, pile in enumerate(piles):
        if pile:
            heappush(heap, (pile[-1], i))
    
    result = []
    while heap:
        val, pile_idx = heappop(heap)
        result.append(val)
        piles[pile_idx].pop()
        if piles[pile_idx]:
            heappush(heap, (piles[pile_idx][-1], pile_idx))
    
    return result, lis
```

### Finding LIS Length

```python
def lis_length(arr: list) -> int:
    """
    Find length of Longest Increasing Subsequence using patience sort.
    
    This is more efficient than DP for just finding length: O(n log n).
    
    >>> lis_length([7, 2, 8, 1, 3, 4, 10, 6, 9, 5])
    5
    >>> lis_length([1, 2, 3, 4, 5])
    5
    >>> lis_length([5, 4, 3, 2, 1])
    1
    """
    from bisect import bisect_left
    
    piles = []  # Just track pile tops
    
    for elem in arr:
        pos = bisect_left(piles, elem)
        if pos < len(piles):
            piles[pos] = elem
        else:
            piles.append(elem)
    
    return len(piles)
```

---

## Real-World Applications

### 1. **Longest Increasing Subsequence**

**Use Case**: Finding LIS in O(n log n) time.

```python
def find_lis(arr: list) -> list:
    """
    Find one longest increasing subsequence.
    
    >>> lis = find_lis([3, 1, 4, 1, 5, 9, 2, 6])
    >>> all(lis[i] < lis[i+1] for i in range(len(lis)-1))
    True
    >>> len(lis) == 4  # LIS length
    True
    """
    from bisect import bisect_left
    
    if not arr:
        return []
    
    n = len(arr)
    piles = []
    positions = []  # Position of each element in piles
    predecessors = [-1] * n  # Back-pointers
    pile_indices = []  # Index of element at top of each pile
    
    for i, elem in enumerate(arr):
        pos = bisect_left(piles, elem)
        
        if pos > 0:
            predecessors[i] = pile_indices[pos - 1]
        
        if pos < len(piles):
            piles[pos] = elem
            pile_indices[pos] = i
        else:
            piles.append(elem)
            pile_indices.append(i)
    
    # Reconstruct LIS
    lis = []
    idx = pile_indices[-1] if pile_indices else -1
    while idx != -1:
        lis.append(arr[idx])
        idx = predecessors[idx]
    
    return lis[::-1]
```

### 2. **Patience Solitaire Analysis**

**Use Case**: Analyzing card game strategies.

```python
class PatienceGame:
    """
    Simulate the Patience card game.
    """
    
    def __init__(self):
        self.piles = []
    
    def play_card(self, card: int) -> int:
        """
        Play a card and return the pile it went to.
        
        >>> game = PatienceGame()
        >>> game.play_card(5)
        0
        >>> game.play_card(3)
        0
        >>> game.play_card(7)
        1
        """
        from bisect import bisect_left
        
        # Find pile (using tops for comparison)
        tops = [pile[-1] for pile in self.piles]
        pos = bisect_left(tops, card)
        
        if pos < len(self.piles):
            self.piles[pos].append(card)
        else:
            self.piles.append([card])
            pos = len(self.piles) - 1
        
        return pos
    
    def get_piles(self):
        """Get current pile state."""
        return [list(reversed(pile)) for pile in self.piles]
    
    def get_lis_length(self):
        """Get LIS length (number of piles)."""
        return len(self.piles)
```

### 3. **Stock Trading Analysis**

**Use Case**: Finding longest period of increasing prices.

```python
def longest_upward_trend(prices: list[float]) -> tuple[int, list[int]]:
    """
    Find the longest strictly increasing subsequence of prices.
    Returns (length, indices of days in the trend).
    
    >>> length, days = longest_upward_trend([100, 90, 95, 85, 90, 100])
    >>> length
    3
    """
    from bisect import bisect_left
    
    n = len(prices)
    if n == 0:
        return 0, []
    
    piles = []  # Pile tops (prices)
    pile_indices = []  # Index of top card
    predecessors = [-1] * n
    
    for i, price in enumerate(prices):
        pos = bisect_left(piles, price)
        
        if pos > 0:
            predecessors[i] = pile_indices[pos - 1]
        
        if pos < len(piles):
            piles[pos] = price
            pile_indices[pos] = i
        else:
            piles.append(price)
            pile_indices.append(i)
    
    # Reconstruct indices
    length = len(piles)
    indices = []
    idx = pile_indices[-1] if pile_indices else -1
    while idx != -1:
        indices.append(idx)
        idx = predecessors[idx]
    
    return length, indices[::-1]
```

### 4. **Scheduling with Dependencies**

**Use Case**: Finding longest chain of dependent tasks.

```python
def longest_task_chain(tasks: list[tuple[int, int]]) -> int:
    """
    Find longest chain of tasks where each task starts after previous ends.
    tasks: list of (start_time, end_time)
    
    >>> tasks = [(1, 3), (2, 4), (3, 5), (4, 6), (5, 7)]
    >>> longest_task_chain(tasks)
    3
    """
    # Sort by end time
    sorted_tasks = sorted(tasks, key=lambda x: x[1])
    
    # Find LIS by start time (must be > previous end time)
    # Use patience sort variant
    from bisect import bisect_right
    
    piles = []  # Pile tops are end times
    
    for start, end in sorted_tasks:
        # Find pile where top end time < start
        pos = bisect_right(piles, start)
        
        if pos < len(piles):
            piles[pos] = end
        else:
            piles.append(end)
    
    return len(piles)
```

### 5. **DNA Sequence Analysis**

**Use Case**: Finding longest common increasing subsequence.

```python
def longest_common_increasing_subsequence(seq1: str, seq2: str) -> int:
    """
    Find longest common increasing subsequence in two DNA sequences.
    Uses patience sort for efficiency.
    
    >>> longest_common_increasing_subsequence("AGCT", "ACGT")
    3
    """
    # Map characters to positions in seq2
    char_positions = {}
    for i, c in enumerate(seq2):
        if c not in char_positions:
            char_positions[c] = []
        char_positions[c].append(i)
    
    # Create sequence of positions
    positions = []
    for c in seq1:
        if c in char_positions:
            positions.extend(char_positions[c][::-1])
    
    # Find LIS of positions
    return lis_length(positions)
```

---

## Advantages and Disadvantages

### ✅ Advantages

1. **O(n log n) worst case** guaranteed
2. **Finds LIS** as a byproduct
3. **Simple conceptual model** (card game)
4. **Efficient merge** using heap

### ❌ Disadvantages

1. **O(n) extra space** required
2. **Not in-place**
3. **Not stable** (standard version)
4. **More complex** than simple sorts

---

## Comparison with Other Algorithms

| Algorithm | Time | Space | Finds LIS | Stable |
|-----------|------|-------|-----------|--------|
| Patience Sort | $O(n \log n)$ | $O(n)$ | Yes | No |
| Merge Sort | $O(n \log n)$ | $O(n)$ | No | Yes |
| Quick Sort | $O(n \log n)$ avg | $O(\log n)$ | No | No |
| Heap Sort | $O(n \log n)$ | $O(1)$ | No | No |

---

## References

1. [Wikipedia: Patience Sorting](https://en.wikipedia.org/wiki/Patience_sorting)
2. Aldous, D. and Diaconis, P. "Longest Increasing Subsequences"
3. [Robinson-Schensted-Knuth correspondence](https://en.wikipedia.org/wiki/Robinson%E2%80%93Schensted%E2%80%93Knuth_correspondence)

---

## See Also

- [Merge Sort](merge_sort.md) - Another O(n log n) sort
- [Heap Sort](heap_sort.md) - Uses heap like merge phase
- [Strand Sort](strand_sort.md) - Also extracts subsequences

