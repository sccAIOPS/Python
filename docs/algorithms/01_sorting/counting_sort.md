# Counting Sort

## Overview

| Property | Value |
|----------|-------|
| **Category** | Sorting Algorithm (Non-Comparison-Based) |
| **Time Complexity** | O(n + k) |
| **Space Complexity** | O(n + k) |
| **Stability** | Yes |
| **In-place** | No |
| **Source File** | [`sorts/counting_sort.py`](../../../sorts/counting_sort.py) |

Where $k$ is the range of input values $(max - min + 1)$.

---

## 1. Mathematical Foundation

### 1.1 Problem Definition

$$
\text{Given: } A = \{a_1, a_2, \ldots, a_n\} \text{ where } a_i \in [min, max] \subset \mathbb{Z}
$$

$$
\text{Find: Sorted permutation } A' \text{ in } O(n + k) \text{ time}
$$

### 1.2 Core Mathematical Concepts

**Key Insight:** If we know how many elements are smaller than or equal to each element, we know its final position.

**Counting Array Property:**

Let $C[i]$ = count of elements equal to $i$

After prefix sum: $C[i]$ = count of elements $\leq i$

Therefore, element with value $v$ belongs at position $C[v] - 1$ (0-indexed).

### 1.3 Key Formulas

**Range Calculation:**
$$
k = max(A) - min(A) + 1
$$

**Final Position:**
$$
\text{position}(a_i) = C[a_i - min] - 1
$$

**Time Complexity:**
$$
T(n, k) = O(n) + O(k) + O(n) = O(n + k)
$$

### 1.4 When is Counting Sort Linear?

Counting Sort achieves O(n) when $k = O(n)$:
- If $k = O(n)$: Total = $O(n + n) = O(n)$
- If $k = O(n^2)$: Total = $O(n + n^2) = O(n^2)$ ❌

---

## 2. Algorithm Description

### 2.1 Intuition

Instead of comparing elements, counting sort:
1. Counts occurrences of each value
2. Computes cumulative counts (prefix sums)
3. Uses counts to place elements directly in correct positions

Think of it as creating "buckets" for each possible value and counting how many elements go in each bucket.

### 2.2 Key Insights

1. **Non-Comparison:** Bypasses O(n log n) lower bound
2. **Stable:** Processes elements in reverse for stability
3. **Limited Range:** Only efficient when $k$ is small relative to $n$
4. **Integer Keys:** Works with discrete, bounded values

### 2.3 Step-by-Step Process

1. Find min and max values
2. Create counting array of size $k = max - min + 1$
3. Count occurrences of each value
4. Compute prefix sums
5. Place elements in output array using counts

---

## 3. Pseudocode

```
ALGORITHM CountingSort(A)
────────────────────────────────────────
INPUT:  A - array of n integers
OUTPUT: Sorted array

1.  IF A is empty THEN RETURN []
2.  
3.  min ← minimum(A)
4.  max ← maximum(A)
5.  k ← max - min + 1
6.  
7.  // Count occurrences
8.  C ← array of k zeros
9.  FOR each x in A DO
10.     C[x - min] ← C[x - min] + 1
11. END FOR
12. 
13. // Compute prefix sums
14. FOR i ← 1 TO k-1 DO
15.     C[i] ← C[i] + C[i-1]
16. END FOR
17. 
18. // Build output (stable: iterate in reverse)
19. output ← array of n elements
20. FOR i ← n-1 DOWNTO 0 DO
21.     value ← A[i]
22.     position ← C[value - min] - 1
23.     output[position] ← value
24.     C[value - min] ← C[value - min] - 1
25. END FOR
26. 
27. RETURN output
────────────────────────────────────────
```

---

## 4. Complexity Analysis

### 4.1 Time Complexity

| Operation | Complexity |
|-----------|------------|
| Find min/max | O(n) |
| Count occurrences | O(n) |
| Prefix sum | O(k) |
| Build output | O(n) |
| **Total** | **O(n + k)** |

### 4.2 Space Complexity

| Type | Complexity |
|------|------------|
| Counting array | O(k) |
| Output array | O(n) |
| **Total** | **O(n + k)** |

---

## 5. Visual Representation

**Input:** `[4, 2, 2, 8, 3, 3, 1]` (min=1, max=8, k=8)

```
Step 1: Count occurrences
Values:   1  2  3  4  5  6  7  8
Counts:  [1, 2, 2, 1, 0, 0, 0, 1]

Step 2: Prefix sums
         [1, 3, 5, 6, 6, 6, 6, 7]
          ↑  ↑  ↑  ↑           ↑
          1  3  5  6           7 elements ≤ 8

Step 3: Place elements (reverse order for stability)
A[6]=1 → position C[0]-1=0 → output[0]=1, C[0]=0
A[5]=3 → position C[2]-1=4 → output[4]=3, C[2]=4
A[4]=3 → position C[2]-1=3 → output[3]=3, C[2]=3
A[3]=8 → position C[7]-1=6 → output[6]=8, C[7]=6
A[2]=2 → position C[1]-1=2 → output[2]=2, C[1]=2
A[1]=2 → position C[1]-1=1 → output[1]=2, C[1]=1
A[0]=4 → position C[3]-1=5 → output[5]=4, C[3]=5

Output: [1, 2, 2, 3, 3, 4, 8]
```

---

## 6. Real-World Applications

### 6.1 Industry Use Cases

| Application | Why Counting Sort |
|-------------|-------------------|
| **Radix Sort subroutine** | Stable, O(n) per digit |
| **Exam score sorting** | Small range (0-100) |
| **Age demographics** | Limited range (0-120) |
| **Byte/character sorting** | k=256 is small |
| **Histogram computation** | Natural fit |

### 6.2 Practical Example

```python
def sort_exam_scores(scores: list[int]) -> list[int]:
    """
    Sort exam scores (0-100) efficiently.
    Counting sort is ideal: n students, k=101 scores.
    
    >>> sort_exam_scores([85, 92, 78, 92, 85, 100, 65])
    [65, 78, 85, 85, 92, 92, 100]
    """
    if not scores:
        return []
    
    # k = 101 for scores 0-100
    counts = [0] * 101
    for score in scores:
        counts[score] += 1
    
    # Reconstruct sorted list
    result = []
    for score, count in enumerate(counts):
        result.extend([score] * count)
    
    return result
```

### 6.3 String/Character Sorting

```python
def counting_sort_string(s: str) -> str:
    """
    Sort characters in a string using counting sort.
    k=128 (ASCII) or k=256 (extended ASCII).
    
    >>> counting_sort_string("dcba")
    'abcd'
    """
    if not s:
        return ""
    
    counts = [0] * 128  # ASCII
    for char in s:
        counts[ord(char)] += 1
    
    result = []
    for ascii_val, count in enumerate(counts):
        result.append(chr(ascii_val) * count)
    
    return ''.join(result)
```

---

## 7. Comparison with Related Algorithms

| Algorithm | Time | Space | Stable | Constraint |
|-----------|------|-------|--------|------------|
| **Counting Sort** | O(n+k) | O(n+k) | ✅ | Small integer range |
| Radix Sort | O(d(n+k)) | O(n+k) | ✅ | Fixed-length integers |
| Bucket Sort | O(n+k) | O(n+k) | ✅ | Uniform distribution |
| Quick Sort | O(n log n) | O(log n) | ❌ | General purpose |

### When to Choose Counting Sort

- ✅ $k = O(n)$ (small range relative to input size)
- ✅ Integer or discrete keys
- ✅ Stability required
- ✅ Subroutine for radix sort
- ❌ Large range ($k >> n$)
- ❌ Floating point numbers
- ❌ Memory constrained

---

## 8. Variants and Optimizations

### 8.1 Handling Negative Numbers

```python
def counting_sort_with_negatives(arr: list[int]) -> list[int]:
    """Handle negative numbers by shifting range."""
    if not arr:
        return []
    
    min_val = min(arr)
    max_val = max(arr)
    range_size = max_val - min_val + 1
    
    # Shift all values to be non-negative
    counts = [0] * range_size
    for x in arr:
        counts[x - min_val] += 1
    
    # Prefix sums and placement...
    # (same as standard algorithm)
```

### 8.2 In-Place Variant

For small ranges, can sort in-place using the counting array to reconstruct:

```python
def counting_sort_inplace(arr: list[int], min_val: int, max_val: int):
    """In-place variant - not stable."""
    counts = [0] * (max_val - min_val + 1)
    for x in arr:
        counts[x - min_val] += 1
    
    idx = 0
    for val in range(min_val, max_val + 1):
        for _ in range(counts[val - min_val]):
            arr[idx] = val
            idx += 1
```

---

## 9. References

- [Wikipedia: Counting Sort](https://en.wikipedia.org/wiki/Counting_sort)
- Cormen, T. H. et al. *Introduction to Algorithms*, Chapter 8.2
- Knuth, D. E. *The Art of Computer Programming*, Vol. 3

---

*Last Updated: December 2024*
*Implementation: [sorts/counting_sort.py](../../../sorts/counting_sort.py)*
