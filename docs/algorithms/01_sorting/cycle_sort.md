# Cycle Sort

## Overview

| Property | Value |
|----------|-------|
| **Category** | Sorting Algorithm |
| **Type** | Comparison-Based (Selection Sort Variant) |
| **Data Structure** | Array |
| **Space Complexity** | O(1) |
| **Stable** | No |
| **In-Place** | Yes |
| **Adaptive** | No |

---

## Mathematical Foundation

### Definition

Cycle Sort is an **in-place, unstable sorting algorithm** that is optimal in terms of the number of memory writes. It is based on the idea that the permutation to be sorted can be decomposed into cycles, and each element can be moved directly to its final position.

### Key Property

**Cycle Sort minimizes the number of memory writes.** This makes it valuable when:
- Memory writes are significantly more expensive than reads (e.g., flash memory, EEPROM)
- Minimizing wear on storage media is important

### Cycle Decomposition

Any permutation can be decomposed into disjoint cycles:

**Example:**
```
Index:    0  1  2  3  4
Array:   [4, 3, 2, 0, 1]
Target:  [0, 1, 2, 3, 4]

Cycle 1: 0 → 4 → 1 → 3 → 0  (length 4)
         A[0]=4 should go to position 4
         A[4]=1 should go to position 1
         A[1]=3 should go to position 3
         A[3]=0 should go to position 0 (cycle complete)

Cycle 2: 2 → 2  (length 1, element already in place)
```

### Mathematical Analysis

**Cycle Length**: For a permutation $\pi$, the cycle containing element $i$ has length equal to the smallest $k$ such that $\pi^k(i) = i$.

**Total Writes**: For a permutation with $c$ cycles of lengths $l_1, l_2, ..., l_c$:

$$\text{writes} = \sum_{i=1}^{c} (l_i - 1) = n - c$$

Where $n$ is the array size and $c$ is the number of cycles (including fixed points).

**Maximum writes:** $n - 1$ (single cycle covering all elements)
**Minimum writes:** $0$ (all elements already in place)

### Write Complexity

**Theorem**: Cycle Sort performs at most $n - 1$ writes, which is optimal.

**Proof**: Each write places exactly one element in its final position. Since at least one element must already be in place (in any cycle), at most $n - 1$ writes are needed.

### Comparison Count

For each element, we count how many elements are smaller to determine its position:

$$\text{position}(a_i) = |\{j : a_j < a_i\}|$$

**Total comparisons:** $O(n^2)$

---

## Pseudocode

```
CYCLE-SORT(A):
    Input: Array A of n elements
    Output: Sorted array A (in-place)
    
    n ← length(A)
    
    // Process each cycle
    for cycle_start ← 0 to n - 2:
        item ← A[cycle_start]
        
        // Find position for item
        pos ← cycle_start
        for i ← cycle_start + 1 to n - 1:
            if A[i] < item:
                pos ← pos + 1
        
        // Item already in correct position
        if pos == cycle_start:
            continue
        
        // Skip duplicates
        while item == A[pos]:
            pos ← pos + 1
        
        // Place item in correct position
        A[pos], item ← item, A[pos]
        
        // Rotate rest of cycle
        while pos ≠ cycle_start:
            pos ← cycle_start
            
            // Find position for displaced item
            for i ← cycle_start + 1 to n - 1:
                if A[i] < item:
                    pos ← pos + 1
            
            // Skip duplicates
            while item == A[pos]:
                pos ← pos + 1
            
            // Place item
            A[pos], item ← item, A[pos]
    
    return A
```

---

## Complexity Analysis

### Time Complexity

| Case | Complexity | Condition |
|------|------------|-----------|
| **Best** | $O(n^2)$ | Any input |
| **Average** | $O(n^2)$ | Any input |
| **Worst** | $O(n^2)$ | Any input |

**Note**: Time complexity is always $O(n^2)$ due to the nested loop for finding positions.

### Write Complexity (Unique Feature)

| Case | Writes | Condition |
|------|--------|-----------|
| **Best** | $0$ | Already sorted |
| **Average** | $n - c$ | $c$ = number of cycles |
| **Worst** | $n - 1$ | Single cycle |

### Space Complexity

| Type | Complexity |
|------|------------|
| Auxiliary | $O(1)$ |
| Total | $O(n)$ (input only) |

### Comparison with Other Sorts

| Algorithm | Time | Writes | Space |
|-----------|------|--------|-------|
| Cycle Sort | $O(n^2)$ | $O(n)$ optimal | $O(1)$ |
| Selection Sort | $O(n^2)$ | $O(n)$ | $O(1)$ |
| Insertion Sort | $O(n^2)$ | $O(n^2)$ | $O(1)$ |
| Heap Sort | $O(n \log n)$ | $O(n \log n)$ | $O(1)$ |
| Merge Sort | $O(n \log n)$ | $O(n \log n)$ | $O(n)$ |

---

## Visual Representation

### Algorithm Flow

```mermaid
flowchart TD
    A[Start at cycle_start = 0] --> B[Get item = A[cycle_start]]
    B --> C[Find correct position for item]
    C --> D{pos == cycle_start?}
    D -->|Yes| E[Item in place, next cycle]
    D -->|No| F[Skip duplicates]
    F --> G[Swap item with A[pos]]
    G --> H{pos == cycle_start?}
    H -->|Yes| E
    H -->|No| I[Find position for new item]
    I --> J[Skip duplicates]
    J --> K[Swap item with A[pos]]
    K --> H
    E --> L{More cycles?}
    L -->|Yes| A
    L -->|No| M[Done]
```

### Visual Example

```
Initial: [4, 3, 2, 1, 0]

Cycle starting at index 0:
  item = 4
  Count elements < 4: {3, 2, 1, 0} = 4 elements
  pos = 4
  
  Swap: A[4] ↔ item
  [4, 3, 2, 1, 0] → [0, 3, 2, 1, 4]
                            ↑
  item = 0 (displaced element)
  
  Count elements < 0: {} = 0 elements
  pos = 0 (= cycle_start, cycle complete!)

Cycle starting at index 1:
  item = 3
  Count elements > 1: {2, 1} = 2 elements (among indices 2,3)
  Wait, count elements < 3: {2, 1} = 2 elements
  pos = 1 + 2 = 3
  
  Swap: A[3] ↔ item
  [0, 3, 2, 1, 4] → [0, 1, 2, 3, 4]
                          ↑
  item = 1 (displaced)
  
  Count elements < 1: {} = 0 elements (among indices 2,3)
  pos = 1 + 0 = 1 (= cycle_start, cycle complete!)

Cycle starting at index 2:
  item = 2
  Count elements < 2: {} = 0 elements (among indices 3)
  pos = 2 (= cycle_start, skip)

Final: [0, 1, 2, 3, 4]
Total writes: 4 (minimum possible for this permutation)
```

### Cycle Visualization

```
Permutation: [4, 0, 3, 1, 2]

Index:  0   1   2   3   4
Value:  4   0   3   1   2

Cycles:
  Cycle 1: 0 → 4 → 2 → 3 → 1 → 0  (5 elements, 4 writes)
  
  0 has value 4, 4 belongs at position 4
  4 has value 2, 2 belongs at position 2
  2 has value 3, 3 belongs at position 3
  3 has value 1, 1 belongs at position 1
  1 has value 0, 0 belongs at position 0 (back to start)

Visual:
    ┌───────────────────────────┐
    │                           │
    ▼                           │
    0 ─── 4 ─┐                  │
              │                  │
              ▼                  │
    1 ◄────── 2                  │
    │                           │
    ▼                           │
    3 ──────────────────────────┘
```

---

## Implementation Details

### Python Implementation

```python
def cycle_sort(array: list) -> list:
    """
    Sort array using Cycle Sort algorithm.
    Minimizes memory writes - optimal for flash memory.
    
    >>> cycle_sort([4, 3, 2, 1])
    [1, 2, 3, 4]
    >>> cycle_sort([-4, 20, 0, -50, 100, -1])
    [-50, -4, -1, 0, 20, 100]
    >>> cycle_sort([-.1, -.2, 1.3, -.8])
    [-0.8, -0.2, -0.1, 1.3]
    >>> cycle_sort([])
    []
    """
    array_len = len(array)
    
    for cycle_start in range(array_len - 1):
        item = array[cycle_start]
        
        # Find position for this item
        pos = cycle_start
        for i in range(cycle_start + 1, array_len):
            if array[i] < item:
                pos += 1
        
        # Item is already in correct position
        if pos == cycle_start:
            continue
        
        # Handle duplicates
        while item == array[pos]:
            pos += 1
        
        # Put item in correct position
        array[pos], item = item, array[pos]
        
        # Rotate rest of the cycle
        while pos != cycle_start:
            pos = cycle_start
            
            for i in range(cycle_start + 1, array_len):
                if array[i] < item:
                    pos += 1
            
            while item == array[pos]:
                pos += 1
            
            array[pos], item = item, array[pos]
    
    return array
```

### Write-Counting Version

```python
def cycle_sort_with_write_count(array: list) -> tuple[list, int]:
    """
    Cycle sort that also returns the number of writes performed.
    """
    writes = 0
    array_len = len(array)
    
    for cycle_start in range(array_len - 1):
        item = array[cycle_start]
        pos = cycle_start
        
        for i in range(cycle_start + 1, array_len):
            if array[i] < item:
                pos += 1
        
        if pos == cycle_start:
            continue
        
        while item == array[pos]:
            pos += 1
        
        array[pos], item = item, array[pos]
        writes += 1
        
        while pos != cycle_start:
            pos = cycle_start
            
            for i in range(cycle_start + 1, array_len):
                if array[i] < item:
                    pos += 1
            
            while item == array[pos]:
                pos += 1
            
            array[pos], item = item, array[pos]
            writes += 1
    
    return array, writes
```

### Edge Cases

| Case | Handling |
|------|----------|
| Empty array | Return empty |
| Single element | Return as-is |
| Already sorted | 0 writes |
| Reverse sorted | Maximum writes |
| All duplicates | Handle with while loop |

---

## Real-World Applications

### 1. **Flash Memory Sorting**

**Use Case**: Sorting data on flash memory/EEPROM where writes are expensive and limited.

```python
class FlashMemorySorter:
    """
    Sort data on flash memory with limited write cycles.
    Cycle sort minimizes writes to extend device lifespan.
    
    Flash memory typically has 10,000-100,000 write cycles per cell.
    """
    
    def __init__(self, max_writes_per_cell=100000):
        self.write_counter = {}
        self.max_writes = max_writes_per_cell
    
    def sort(self, data: list) -> list:
        """Sort with minimal writes."""
        n = len(data)
        
        for cycle_start in range(n - 1):
            item = data[cycle_start]
            pos = cycle_start
            
            # Read operations (cheap on flash)
            for i in range(cycle_start + 1, n):
                if data[i] < item:
                    pos += 1
            
            if pos == cycle_start:
                continue
            
            while item == data[pos]:
                pos += 1
            
            # Write operation (expensive on flash)
            self._write(pos, data, item)
            data[pos], item = item, data[pos]
            
            while pos != cycle_start:
                pos = cycle_start
                for i in range(cycle_start + 1, n):
                    if data[i] < item:
                        pos += 1
                
                while item == data[pos]:
                    pos += 1
                
                self._write(pos, data, item)
                data[pos], item = item, data[pos]
        
        return data
    
    def _write(self, pos, data, value):
        """Track writes per position."""
        self.write_counter[pos] = self.write_counter.get(pos, 0) + 1
        if self.write_counter[pos] > self.max_writes:
            raise RuntimeError(f"Write limit exceeded at position {pos}")
```

### 2. **SSD Optimization**

**Use Case**: Database index sorting on SSDs where write amplification matters.

```python
class SSDFriendlySorter:
    """
    Sort database indices with minimal SSD writes.
    Reduces write amplification and extends SSD lifespan.
    """
    
    def sort_index(self, index_entries: list) -> list:
        """
        Sort index entries by key.
        Uses cycle sort for minimal writes.
        """
        n = len(index_entries)
        
        for cycle_start in range(n - 1):
            item = index_entries[cycle_start]
            pos = cycle_start
            
            for i in range(cycle_start + 1, n):
                if index_entries[i].key < item.key:
                    pos += 1
            
            if pos == cycle_start:
                continue
            
            while item.key == index_entries[pos].key:
                pos += 1
            
            index_entries[pos], item = item, index_entries[pos]
            
            while pos != cycle_start:
                pos = cycle_start
                for i in range(cycle_start + 1, n):
                    if index_entries[i].key < item.key:
                        pos += 1
                
                while item.key == index_entries[pos].key:
                    pos += 1
                
                index_entries[pos], item = item, index_entries[pos]
        
        return index_entries
```

### 3. **Embedded Systems with EEPROM**

**Use Case**: IoT devices storing configuration data.

```python
class EEPROMConfigManager:
    """
    Manage sorted configuration entries in EEPROM.
    EEPROM has very limited write cycles (~100,000).
    """
    
    def __init__(self, eeprom_interface):
        self.eeprom = eeprom_interface
        self.entries = []
    
    def sort_entries(self):
        """Sort configuration entries with minimal writes."""
        n = len(self.entries)
        writes = 0
        
        for cycle_start in range(n - 1):
            item = self.entries[cycle_start]
            pos = cycle_start
            
            for i in range(cycle_start + 1, n):
                if self.entries[i].priority < item.priority:
                    pos += 1
            
            if pos == cycle_start:
                continue
            
            while item.priority == self.entries[pos].priority:
                pos += 1
            
            # Minimize EEPROM writes
            self.entries[pos], item = item, self.entries[pos]
            self.eeprom.write(pos, self.entries[pos])
            writes += 1
            
            while pos != cycle_start:
                pos = cycle_start
                for i in range(cycle_start + 1, n):
                    if self.entries[i].priority < item.priority:
                        pos += 1
                
                while item.priority == self.entries[pos].priority:
                    pos += 1
                
                self.entries[pos], item = item, self.entries[pos]
                self.eeprom.write(pos, self.entries[pos])
                writes += 1
        
        return writes
```

### 4. **Genetic Algorithm Chromosome Sorting**

**Use Case**: Sorting chromosomes while preserving mutation information.

```python
class GeneticSorter:
    """
    Sort genetic algorithm chromosomes by fitness.
    Minimize memory operations to track lineage.
    """
    
    def sort_by_fitness(self, population: list) -> tuple[list, list]:
        """
        Sort population by fitness, return swap history.
        """
        n = len(population)
        swap_history = []
        
        for cycle_start in range(n - 1):
            item = population[cycle_start]
            pos = cycle_start
            
            for i in range(cycle_start + 1, n):
                if population[i].fitness > item.fitness:  # Higher fitness first
                    pos += 1
            
            if pos == cycle_start:
                continue
            
            while item.fitness == population[pos].fitness:
                pos += 1
            
            population[pos], item = item, population[pos]
            swap_history.append((cycle_start, pos))
            
            while pos != cycle_start:
                pos = cycle_start
                for i in range(cycle_start + 1, n):
                    if population[i].fitness > item.fitness:
                        pos += 1
                
                while item.fitness == population[pos].fitness:
                    pos += 1
                
                old_pos = pos
                population[pos], item = item, population[pos]
                swap_history.append((old_pos, pos))
        
        return population, swap_history
```

---

## When to Use Cycle Sort

### ✅ Ideal Scenarios

1. **Flash memory / EEPROM** - Write cycles are limited
2. **SSD optimization** - Minimize write amplification
3. **Memory wear leveling** - Extend storage lifespan
4. **Small datasets** - Where $O(n^2)$ is acceptable
5. **Write cost >> Read cost** - Asymmetric access costs

### ❌ Avoid When

1. **Large datasets** - $O(n^2)$ time is too slow
2. **Stability required** - Cycle sort is not stable
3. **RAM-based sorting** - Writes are as cheap as reads
4. **Time-critical applications** - Use faster algorithms

---

## Theoretical Optimality

### Theorem: Write Optimality

**Claim**: Cycle sort achieves the minimum possible number of writes for in-place sorting.

**Proof Sketch**:
1. Each element must be written to its final position at least once (if not already there)
2. Cycle sort writes each element exactly once to its final position
3. Therefore, it achieves the minimum

### Lower Bound

For a permutation with $c$ cycles (including trivial cycles):
$$\text{minimum writes} = n - c$$

Cycle sort achieves this bound exactly.

---

## References

1. [Wikipedia: Cycle Sort](https://en.wikipedia.org/wiki/Cycle_sort)
2. Haddon, B.K. (1990). "Cycle-Sort: A Linear Sorting Method"
3. Flash Memory Best Practices - Intel Developer Zone

---

## See Also

- [Selection Sort](selection_sort.md) - Similar complexity, more writes
- [Heap Sort](heap_sort.md) - Better time complexity, more writes
- [In-Place Merge Sort](in_place_merge_sort.md) - O(n log n) time, O(n log n) writes

