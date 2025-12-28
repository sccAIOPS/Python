# Selection Sort

## Overview

| Property | Value |
|----------|-------|
| **Category** | Sorting Algorithm (Comparison-Based) |
| **Time Complexity (Best)** | O(n²) |
| **Time Complexity (Average)** | O(n²) |
| **Time Complexity (Worst)** | O(n²) |
| **Space Complexity** | O(1) |
| **Stability** | No |
| **In-place** | Yes |
| **Adaptive** | No |
| **Source File** | [`sorts/selection_sort.py`](../../../sorts/selection_sort.py) |

---

## 1. Mathematical Foundation

### 1.1 Problem Definition

$$
\text{Given: } A = \{a_1, a_2, \ldots, a_n\}
$$

$$
\text{Find permutation where: } a'_1 \leq a'_2 \leq \ldots \leq a'_n
$$

### 1.2 Core Mathematical Concepts

**Selection Principle:**

In iteration $i$, find the minimum element in the unsorted portion $A[i..n-1]$ and place it at position $i$.

$$
\min_{i \leq j < n} A[j] \rightarrow A[i]
$$

### 1.3 Key Formulas

**Number of Comparisons (All Cases):**

$$
C(n) = \sum_{i=0}^{n-2} (n-1-i) = \sum_{k=1}^{n-1} k = \frac{n(n-1)}{2} = O(n^2)
$$

**Number of Swaps:**

$$
S(n) = n - 1 = O(n)
$$

This makes Selection Sort optimal when writes/swaps are expensive.

### 1.4 Proof of Correctness

**Loop Invariant:** At the start of iteration $i$, the subarray $A[0..i-1]$ contains the $i$ smallest elements in sorted order, in their final positions.

**Proof:**
- **Initialization:** Empty prefix is trivially sorted
- **Maintenance:** We find minimum in $A[i..n-1]$ and swap to $A[i]$
- **Termination:** After $n-1$ iterations, array is sorted

---

## 2. Algorithm Description

### 2.1 Intuition

Selection Sort repeatedly finds the minimum element from the unsorted portion and moves it to the sorted portion. Like selecting cards from a scattered pile one by one in order.

### 2.2 Key Insights

1. **Minimum Swaps:** Only O(n) swaps (good for expensive writes)
2. **Not Adaptive:** Always O(n²) regardless of input
3. **Simple Implementation:** Easy to understand and code
4. **Not Stable:** Swapping can change relative order of equal elements

### 2.3 Step-by-Step Process

1. Find minimum element in unsorted portion
2. Swap it with first unsorted element
3. Move boundary between sorted/unsorted
4. Repeat until sorted

---

## 3. Pseudocode

```
ALGORITHM SelectionSort(A)
────────────────────────────────────────
INPUT:  A - array of n elements
OUTPUT: A sorted in ascending order

1.  n ← length(A)
2.  
3.  FOR i ← 0 TO n-2 DO
4.      minIndex ← i
5.      
6.      // Find minimum in unsorted portion
7.      FOR j ← i+1 TO n-1 DO
8.          IF A[j] < A[minIndex] THEN
9.              minIndex ← j
10.         END IF
11.     END FOR
12.     
13.     // Swap if needed
14.     IF minIndex ≠ i THEN
15.         SWAP(A[i], A[minIndex])
16.     END IF
17. END FOR
18. 
19. RETURN A
────────────────────────────────────────
```

---

## 4. Complexity Analysis

### 4.1 Time Complexity

| Case | Complexity | Condition |
|------|------------|-----------|
| **Best** | O(n²) | All cases (not adaptive) |
| **Average** | O(n²) | All cases |
| **Worst** | O(n²) | All cases |

### 4.2 Space Complexity

| Type | Complexity |
|------|------------|
| **Auxiliary** | O(1) |
| **Total** | O(n) |

---

## 5. Visual Representation

**Input:** `[64, 25, 12, 22, 11]`

```
i=0: [64, 25, 12, 22, 11]
      ↑               ↑
      i              min=11
     Swap: [11, 25, 12, 22, 64]
            ─
          sorted

i=1: [11, 25, 12, 22, 64]
          ↑   ↑
          i  min=12
     Swap: [11, 12, 25, 22, 64]
            ─────
            sorted

i=2: [11, 12, 25, 22, 64]
              ↑   ↑
              i  min=22
     Swap: [11, 12, 22, 25, 64]
            ────────
             sorted

i=3: [11, 12, 22, 25, 64]
                  ↑   ↑
                  i  min=25 (same)
     No swap needed
     
Result: [11, 12, 22, 25, 64]
```

---

## 6. Real-World Applications

### 6.1 When Selection Sort Excels

| Scenario | Reason |
|----------|--------|
| **Flash memory** | Minimum writes (O(n) swaps) |
| **Linked lists** | No random access needed for finding min |
| **Small arrays** | Simple and sufficient |
| **Checking sorted** | Side effect: verifies sortedness |

### 6.2 Practical Example

```python
def selection_sort_minimize_writes(data: list, write_cost: float) -> tuple:
    """
    When writes are expensive (flash memory, network), 
    selection sort's O(n) swaps is optimal.
    
    Returns: (sorted_data, write_count)
    """
    n = len(data)
    writes = 0
    
    for i in range(n - 1):
        min_idx = i
        for j in range(i + 1, n):
            if data[j] < data[min_idx]:
                min_idx = j
        
        if min_idx != i:
            data[i], data[min_idx] = data[min_idx], data[i]
            writes += 2  # Each swap = 2 writes
    
    return data, writes
```

---

## 7. Comparison with Related Algorithms

| Algorithm | Comparisons | Swaps | Stable |
|-----------|-------------|-------|--------|
| **Selection Sort** | O(n²) | O(n) | ❌ |
| Bubble Sort | O(n²) | O(n²) | ✅ |
| Insertion Sort | O(n²) | O(n²) | ✅ |

### When to Choose Selection Sort

- ✅ Minimizing memory writes
- ✅ Simple implementation needed
- ✅ Auxiliary memory not available
- ❌ Stability required
- ❌ Large datasets

---

## 8. Variants

1. **Bidirectional Selection Sort:** Find both min and max in each pass, reducing passes by half

2. **Stable Selection Sort:** Use insertion instead of swap - increases swaps to O(n²)

3. **Heap Sort:** Generalization using heap structure - improves to O(n log n)

---

## 9. References

- [Wikipedia: Selection Sort](https://en.wikipedia.org/wiki/Selection_sort)
- Knuth, D.E. *The Art of Computer Programming*, Vol 3

---

*Last Updated: December 2024*
*Implementation: [sorts/selection_sort.py](../../../sorts/selection_sort.py)*
