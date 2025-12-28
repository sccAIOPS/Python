# Insertion Sort

## Overview

| Property | Value |
|----------|-------|
| **Category** | Sorting Algorithm (Comparison-Based) |
| **Time Complexity (Best)** | O(n) |
| **Time Complexity (Average)** | O(n²) |
| **Time Complexity (Worst)** | O(n²) |
| **Space Complexity** | O(1) |
| **Stability** | Yes |
| **In-place** | Yes |
| **Adaptive** | Yes |
| **Online** | Yes |
| **Source File** | [`sorts/insertion_sort.py`](../../../sorts/insertion_sort.py) |

---

## 1. Mathematical Foundation

### 1.1 Problem Definition

$$
\text{Sort } A = \{a_1, a_2, \ldots, a_n\} \text{ into } A' \text{ where } a'_i \leq a'_{i+1}
$$

### 1.2 Core Mathematical Concepts

**Inversions:**

The number of inversions measures how "unsorted" an array is:
$$
\text{inv}(A) = |\{(i,j) : i < j \text{ and } A[i] > A[j]\}|
$$

Insertion Sort performs exactly one swap per inversion.

**Running Time Relation:**
$$
T(n) = \Theta(n + \text{inv}(A))
$$

**Maximum Inversions (Reverse Sorted):**
$$
\text{inv}_{\max} = \frac{n(n-1)}{2}
$$

### 1.3 Key Formulas

**Best Case (Sorted):**
$$
T_{\text{best}}(n) = \sum_{i=1}^{n-1} 1 = n - 1 = O(n)
$$

**Worst Case (Reverse Sorted):**
$$
T_{\text{worst}}(n) = \sum_{i=1}^{n-1} i = \frac{n(n-1)}{2} = O(n^2)
$$

**Average Case:**
$$
T_{\text{avg}}(n) = \frac{n(n-1)}{4} = O(n^2)
$$

### 1.4 Proof of Correctness

**Loop Invariant:** At the start of each iteration of the outer loop, the subarray $A[0..i-1]$ consists of the elements originally in $A[0..i-1]$ but in sorted order.

**Proof:**
- **Initialization:** When $i = 1$, $A[0..0]$ contains one element, trivially sorted.
- **Maintenance:** The inner loop shifts elements larger than $A[i]$ one position right, then inserts $A[i]$ in correct position.
- **Termination:** When $i = n$, $A[0..n-1]$ is sorted.

---

## 2. Algorithm Description

### 2.1 Intuition

Insertion Sort works like sorting playing cards in your hand:
1. Start with one card (sorted)
2. Take the next card
3. Insert it in the correct position among the sorted cards
4. Repeat until all cards are sorted

### 2.2 Key Insights

1. **Adaptive:** O(n) for nearly sorted data
2. **Online:** Can sort as elements are received
3. **Stable:** Equal elements maintain relative order
4. **Small Overhead:** Excellent for small arrays

### 2.3 Step-by-Step Process

1. Start from the second element (index 1)
2. Store current element as `key`
3. Compare `key` with sorted portion, shifting larger elements right
4. Insert `key` in correct position
5. Repeat for all elements

---

## 3. Pseudocode

```
ALGORITHM InsertionSort(A)
────────────────────────────────────────
INPUT:  A - array of n comparable elements
OUTPUT: A sorted in ascending order

1.  FOR i ← 1 TO n-1 DO
2.      key ← A[i]
3.      j ← i - 1
4.      
5.      WHILE j ≥ 0 AND A[j] > key DO
6.          A[j + 1] ← A[j]  // Shift right
7.          j ← j - 1
8.      END WHILE
9.      
10.     A[j + 1] ← key  // Insert
11. END FOR
12. 
13. RETURN A
────────────────────────────────────────
```

---

## 4. Complexity Analysis

### 4.1 Time Complexity

| Case | Complexity | Condition |
|------|------------|-----------|
| **Best** | O(n) | Already sorted |
| **Average** | O(n²) | Random order |
| **Worst** | O(n²) | Reverse sorted |

### 4.2 Space Complexity

| Type | Complexity |
|------|------------|
| **Auxiliary** | O(1) |
| **Total** | O(n) |

---

## 5. Visual Representation

**Input:** `[5, 2, 4, 6, 1, 3]`

```
Initial:   [5, 2, 4, 6, 1, 3]
            ↑
           sorted

i=1:       [5, 2, 4, 6, 1, 3]  key=2
           [2, 5, 4, 6, 1, 3]  Insert 2 before 5
            ─────
           sorted

i=2:       [2, 5, 4, 6, 1, 3]  key=4
           [2, 4, 5, 6, 1, 3]  Insert 4 between 2,5
            ────────
            sorted

i=3:       [2, 4, 5, 6, 1, 3]  key=6
           [2, 4, 5, 6, 1, 3]  6 already in place
            ───────────
              sorted

i=4:       [2, 4, 5, 6, 1, 3]  key=1
           [1, 2, 4, 5, 6, 3]  Insert 1 at start
            ──────────────
                sorted

i=5:       [1, 2, 4, 5, 6, 3]  key=3
           [1, 2, 3, 4, 5, 6]  Insert 3
            ─────────────────
                  sorted
```

---

## 6. Real-World Applications

### 6.1 Industry Use Cases

| Application | Why Insertion Sort |
|-------------|-------------------|
| **Small subarrays** | Tim Sort uses it for n < 64 |
| **Nearly sorted data** | O(n) performance |
| **Online sorting** | Process elements as they arrive |
| **Embedded systems** | Simple, low overhead |

### 6.2 Practical Example

```python
def insertion_sort_with_key(items: list, key=None) -> list:
    """
    Sort with custom key function - used in real applications.
    
    >>> insertion_sort_with_key(['banana', 'apple', 'cherry'], key=len)
    ['apple', 'banana', 'cherry']
    """
    key = key or (lambda x: x)
    for i in range(1, len(items)):
        current = items[i]
        j = i - 1
        while j >= 0 and key(items[j]) > key(current):
            items[j + 1] = items[j]
            j -= 1
        items[j + 1] = current
    return items
```

---

## 7. Comparison with Related Algorithms

| Algorithm | Best | Average | Space | Stable |
|-----------|------|---------|-------|--------|
| **Insertion Sort** | O(n) | O(n²) | O(1) | ✅ |
| Bubble Sort | O(n) | O(n²) | O(1) | ✅ |
| Selection Sort | O(n²) | O(n²) | O(1) | ❌ |

### When to Choose Insertion Sort

- ✅ Small arrays (n < 50)
- ✅ Nearly sorted data
- ✅ Online/streaming data
- ✅ Stability required
- ❌ Large random datasets

---

## 8. Variants

1. **Binary Insertion Sort:** Use binary search to find insertion point - O(n log n) comparisons but still O(n²) shifts

2. **Shell Sort:** Generalization with gap sequences - improves to O(n^1.25) average

---

## 9. References

- [Wikipedia: Insertion Sort](https://en.wikipedia.org/wiki/Insertion_sort)
- Cormen, T. H. et al. *Introduction to Algorithms*, Chapter 2.1

---

*Last Updated: December 2024*
*Implementation: [sorts/insertion_sort.py](../../../sorts/insertion_sort.py)*
