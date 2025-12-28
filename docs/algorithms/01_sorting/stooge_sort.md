# Stooge Sort

## Overview

| Property | Value |
|----------|-------|
| **Category** | Sorting Algorithm |
| **Type** | Comparison-Based (Recursive) |
| **Data Structure** | Array |
| **Space Complexity** | O(log n) |
| **Stable** | No |
| **In-Place** | Yes |
| **Adaptive** | No |

---

## Mathematical Foundation

### Definition

Stooge Sort is a **recursive sorting algorithm** with a remarkably bad time complexity. It works by:
1. Swapping the first and last elements if they are in the wrong order
2. Recursively sorting the first 2/3 of the array
3. Recursively sorting the last 2/3 of the array
4. Recursively sorting the first 2/3 again

### Origin

Named after "The Three Stooges" comedy team, reflecting the algorithm's comically inefficient approach of doing the same work multiple times.

### Algorithm Logic

The key insight is that by sorting overlapping 2/3 portions three times:
- First 2/3 sort: Moves small elements toward the beginning
- Last 2/3 sort: Moves large elements toward the end, but may displace sorted elements
- First 2/3 again: Fixes any elements displaced by the second sort

### Correctness Proof

**Claim**: After Stooge Sort, all elements are in sorted order.

**Proof by Induction**:

*Base Case*: For $n \leq 2$, a single comparison and potential swap suffices.

*Inductive Step*: Assume Stooge Sort correctly sorts arrays of size $< n$.

For array $A[0..n-1]$:
1. After step 1: $A[0] \leq A[n-1]$
2. Let $t = \lfloor n/3 \rfloor$
3. After sorting $A[0..n-t-1]$: First 2/3 is sorted
4. After sorting $A[t..n-1]$: Last 2/3 is sorted, containing the largest $\lceil 2n/3 \rceil$ elements
5. After sorting $A[0..n-t-1]$ again: The smallest $\lfloor 2n/3 \rfloor$ elements are sorted

Since the arrays overlap by at least $n/3$ elements, the entire array becomes sorted.

### Recurrence Relation

$$T(n) = 3T\left(\frac{2n}{3}\right) + O(1)$$

Using the Master Theorem with $a = 3$, $b = 3/2$:

$$\log_{3/2} 3 = \frac{\log 3}{\log(3/2)} \approx \frac{1.585}{0.405} \approx 2.71$$

**Solution:**
$$T(n) = O(n^{\log_{3/2} 3}) = O(n^{2.71})$$

More precisely:
$$T(n) = O(n^{\log_2 3 / \log_2(3/2)}) = O(n^{2.7095...})$$

---

## Pseudocode

```
STOOGE-SORT(A, i, j):
    Input: Array A, indices i (start) and j (end)
    Output: Sorted subarray A[i..j]
    
    // Base case: 0 or 1 elements
    if i >= j:
        return
    
    // Step 1: Compare and swap first and last
    if A[i] > A[j]:
        swap(A[i], A[j])
    
    // Step 2: If more than 2 elements
    if j - i + 1 > 2:
        t ← (j - i + 1) / 3
        
        // Sort first 2/3
        STOOGE-SORT(A, i, j - t)
        
        // Sort last 2/3
        STOOGE-SORT(A, i + t, j)
        
        // Sort first 2/3 again
        STOOGE-SORT(A, i, j - t)
```

---

## Complexity Analysis

### Time Complexity

| Case | Complexity | Notes |
|------|------------|-------|
| **Best** | $O(n^{2.71})$ | Always same |
| **Average** | $O(n^{2.71})$ | Always same |
| **Worst** | $O(n^{2.71})$ | Always same |

### Detailed Analysis

The recurrence $T(n) = 3T(2n/3) + 1$ gives us:

$$T(n) = 3^k \cdot T\left(\frac{2^k \cdot n}{3^k}\right) + \sum_{i=0}^{k-1} 3^i$$

At the bottom level where $\frac{2^k \cdot n}{3^k} = 1$:

$$k = \log_{3/2} n = \frac{\log n}{\log(3/2)}$$

Number of base operations: $3^k = 3^{\log_{3/2} n} = n^{\log_{3/2} 3}$

### Space Complexity

| Type | Complexity |
|------|------------|
| Recursive calls | $O(\log n)$ |
| Per call | $O(1)$ |
| **Total** | $O(\log n)$ |

### Comparison with Other Sorts

| Algorithm | Time Complexity | Growth for n=1000 |
|-----------|-----------------|-------------------|
| Stooge Sort | $O(n^{2.71})$ | ~172,000,000 |
| Bubble Sort | $O(n^2)$ | 1,000,000 |
| Selection Sort | $O(n^2)$ | 1,000,000 |
| Merge Sort | $O(n \log n)$ | ~10,000 |

Stooge Sort is **worse than O(n²)** algorithms!

---

## Visual Representation

### Algorithm Flow

```mermaid
flowchart TD
    A[Start: STOOGE i, j] --> B{i >= j?}
    B -->|Yes| C[Return - Base case]
    B -->|No| D{A[i] > A[j]?}
    D -->|Yes| E[Swap A[i], A[j]]
    D -->|No| F{j - i + 1 > 2?}
    E --> F
    F -->|No| G[Return - Only 2 elements]
    F -->|Yes| H[t = length / 3]
    H --> I[STOOGE first 2/3]
    I --> J[STOOGE last 2/3]
    J --> K[STOOGE first 2/3 again]
    K --> L[Return]
```

### Visual Example

```
Initial: [2, 4, 1, 3, 5]
n = 5, t = 5/3 = 1

Step 1: Compare A[0]=2 with A[4]=5: OK (2 < 5)

STOOGE(A, 0, 3) - First 2/3: [2, 4, 1, 3]
  Step 1: Compare A[0]=2 with A[3]=3: OK
  n = 4, t = 4/3 = 1
  
  STOOGE(A, 0, 2) - [2, 4, 1]
    Step 1: Compare A[0]=2 with A[2]=1: SWAP → [1, 4, 2]
    n = 3, t = 1
    STOOGE(A, 0, 1) - [1, 4] → OK
    STOOGE(A, 1, 2) - [4, 2] → SWAP → [2, 4]
    STOOGE(A, 0, 1) - [1, 2] → OK
    Result: [1, 2, 4]
    
  Array now: [1, 2, 4, 3, 5]
  
  STOOGE(A, 1, 3) - [2, 4, 3]
    Step 1: Compare A[1]=2 with A[3]=3: OK
    STOOGE(A, 1, 2) - [2, 4] → OK
    STOOGE(A, 2, 3) - [4, 3] → SWAP → [3, 4]
    STOOGE(A, 1, 2) - [2, 3] → OK
    Result: [2, 3, 4]
    
  Array now: [1, 2, 3, 4, 5]
  
  STOOGE(A, 0, 2) - [1, 2, 3] → Already sorted

STOOGE(A, 1, 4) - Last 2/3: [2, 3, 4, 5] → Already sorted

STOOGE(A, 0, 3) - First 2/3 again: [1, 2, 3, 4] → Already sorted

Final: [1, 2, 3, 4, 5] ✓
```

### Recursion Tree (for n=9)

```
                    STOOGE(0,8)
                   /    |    \
                  /     |     \
          STOOGE(0,5) STOOGE(3,8) STOOGE(0,5)
          /   |   \    /   |   \   /   |   \
         ... ... ...  ... ... ... ... ... ...

Each node spawns 3 children
Total nodes = 3^k where k ≈ log_{3/2}(n)
```

---

## Implementation Details

### Python Implementation

```python
def stooge_sort(arr: list[int]) -> list[int]:
    """
    Sort array using Stooge Sort algorithm.
    
    Examples:
    >>> stooge_sort([18.1, 0, -7.1, -1, 2, 2])
    [-7.1, -1, 0, 2, 2, 18.1]
    >>> stooge_sort([])
    []
    """
    stooge(arr, 0, len(arr) - 1)
    return arr


def stooge(arr: list[int], i: int, h: int) -> None:
    """Recursive stooge sort helper."""
    if i >= h:
        return
    
    # Swap first and last if needed
    if arr[i] > arr[h]:
        arr[i], arr[h] = arr[h], arr[i]
    
    # If more than 2 elements
    if h - i + 1 > 2:
        t = (h - i + 1) // 3
        
        # Sort first 2/3
        stooge(arr, i, h - t)
        
        # Sort last 2/3
        stooge(arr, i + t, h)
        
        # Sort first 2/3 again
        stooge(arr, i, h - t)
```

### Counting Operations

```python
def stooge_sort_with_count(arr: list[int]) -> tuple[list[int], int]:
    """Stooge sort with operation count."""
    count = [0]  # Use list for mutable counter
    
    def stooge(i: int, h: int) -> None:
        count[0] += 1  # Count calls
        
        if i >= h:
            return
        
        if arr[i] > arr[h]:
            arr[i], arr[h] = arr[h], arr[i]
        
        if h - i + 1 > 2:
            t = (h - i + 1) // 3
            stooge(i, h - t)
            stooge(i + t, h)
            stooge(i, h - t)
    
    stooge(0, len(arr) - 1)
    return arr, count[0]
```

### Edge Cases

| Case | Handling |
|------|----------|
| Empty array | Returns empty |
| Single element | Returns as-is |
| Two elements | One comparison/swap |
| Already sorted | Same operations |
| Reverse sorted | Same operations |

---

## Real-World Applications

### ⚠️ Important Note

**Stooge Sort should NEVER be used in production code.** It exists for:
1. Educational purposes
2. Algorithm complexity analysis
3. Understanding recursion
4. As a cautionary example

### 1. **Teaching Recursion and Complexity**

**Use Case**: Demonstrating how bad algorithms can be.

```python
class RecursionTeacher:
    """
    Use Stooge Sort to teach:
    - Recursion concepts
    - Master theorem application
    - Why algorithm analysis matters
    """
    
    def demonstrate_bad_recursion(self, n_values=[5, 10, 15, 20]):
        """Show exponential growth of operations."""
        import time
        
        for n in n_values:
            arr = list(range(n, 0, -1))  # Worst case
            
            start = time.time()
            stooge_sort(arr)
            elapsed = time.time() - start
            
            # Calculate theoretical operations
            theoretical = n ** 2.71
            
            print(f"n={n:2d}: Time={elapsed:.4f}s, "
                  f"Theoretical O(n^2.71)≈{theoretical:.0f}")
    
    def compare_algorithms(self, n=15):
        """Compare Stooge Sort with better algorithms."""
        import time
        
        arr = list(range(n, 0, -1))
        
        algorithms = {
            'Stooge Sort': stooge_sort,
            'Bubble Sort': lambda a: sorted(a),  # Using built-in for comparison
            'Python Sort': sorted,
        }
        
        for name, algo in algorithms.items():
            test_arr = arr.copy()
            start = time.time()
            algo(test_arr)
            elapsed = time.time() - start
            print(f"{name}: {elapsed:.6f}s")
```

### 2. **Algorithm Analysis Exercises**

**Use Case**: Practice with unusual recurrence relations.

```python
class AlgorithmAnalysis:
    """
    Analyze Stooge Sort as an exercise in:
    - Solving recurrence relations
    - Master theorem application
    - Asymptotic analysis
    """
    
    @staticmethod
    def solve_recurrence():
        """
        Recurrence: T(n) = 3T(2n/3) + O(1)
        
        Using Master Theorem:
        a = 3 (number of subproblems)
        b = 3/2 (size reduction factor)
        f(n) = O(1) (work per level)
        
        log_b(a) = log_{3/2}(3) = ln(3)/ln(3/2) ≈ 2.71
        
        Since f(n) = O(1) = O(n^0) and 0 < 2.71:
        T(n) = Θ(n^{log_{3/2}(3)}) = Θ(n^{2.71})
        """
        from math import log
        
        a = 3
        b = 3/2
        critical_exp = log(a) / log(b)
        
        return {
            'a': a,
            'b': b,
            'critical_exponent': critical_exp,
            'complexity': f'O(n^{critical_exp:.4f})'
        }
    
    @staticmethod
    def empirical_analysis(max_n=100):
        """Verify theoretical complexity empirically."""
        import time
        from math import log
        
        results = []
        for n in range(10, max_n, 10):
            arr = list(range(n, 0, -1))
            
            start = time.time()
            stooge_sort(arr)
            elapsed = time.time() - start
            
            # Estimate exponent from time
            if n > 10:
                prev = results[-1]
                ratio = elapsed / prev['time']
                size_ratio = n / prev['n']
                estimated_exp = log(ratio) / log(size_ratio)
                results.append({'n': n, 'time': elapsed, 'exp': estimated_exp})
            else:
                results.append({'n': n, 'time': elapsed, 'exp': None})
        
        return results
```

### 3. **Understanding Overlapping Subproblems**

**Use Case**: Teaching why repeated work is inefficient.

```python
def stooge_with_visualization(arr, depth=0):
    """
    Visualize the recursive calls to show overlapping work.
    """
    indent = "  " * depth
    
    def stooge_viz(i, h, depth):
        indent = "  " * depth
        print(f"{indent}STOOGE({i}, {h}): {arr[i:h+1]}")
        
        if i >= h:
            return
        
        if arr[i] > arr[h]:
            arr[i], arr[h] = arr[h], arr[i]
            print(f"{indent}  Swapped: {arr[i:h+1]}")
        
        if h - i + 1 > 2:
            t = (h - i + 1) // 3
            print(f"{indent}  First 2/3: indices {i} to {h-t}")
            stooge_viz(i, h - t, depth + 1)
            print(f"{indent}  Last 2/3: indices {i+t} to {h}")
            stooge_viz(i + t, h, depth + 1)
            print(f"{indent}  First 2/3 AGAIN: indices {i} to {h-t}")
            stooge_viz(i, h - t, depth + 1)
    
    stooge_viz(0, len(arr) - 1, 0)
    return arr
```

---

## Why Stooge Sort is Interesting

### 1. Unusual Complexity Class

Most sorting algorithms are either:
- $O(n \log n)$ - optimal comparison sorts
- $O(n^2)$ - simple quadratic sorts
- $O(n!)$ - pathological sorts like Bogo Sort

Stooge Sort sits in a unique position at $O(n^{2.71})$.

### 2. Master Theorem Application

It's an excellent example for applying the Master Theorem with fractional subproblem sizes.

### 3. Recursive Thinking

It demonstrates that "more recursion" doesn't mean "better algorithm."

---

## When to Use Stooge Sort

### ✅ Acceptable Uses

1. **Never in production**
2. **Teaching algorithm analysis**
3. **Master theorem examples**
4. **Code golf/humor**

### ❌ Never Use For

1. **Any real sorting task**
2. **Any performance-sensitive code**
3. **Any array larger than ~10 elements**

---

## Comparison with Other "Bad" Sorts

| Algorithm | Time | Notes |
|-----------|------|-------|
| Stooge Sort | $O(n^{2.71})$ | Worse than quadratic |
| Bubble Sort | $O(n^2)$ | Simple quadratic |
| Bogo Sort | $O(n \cdot n!)$ | Expected time |
| Sleep Sort | $O(max(arr))$ | Not a real sort |
| Slowsort | $O(n^{\log n})$ | Multiply and surrender |

---

## References

1. Cormen, T.H. et al. "Introduction to Algorithms" - Problem 7-3
2. [Wikipedia: Stooge Sort](https://en.wikipedia.org/wiki/Stooge_sort)
3. Knuth, D.E. "The Art of Computer Programming"

---

## See Also

- [Bogo Sort](bogo_sort.md) - Even worse expected time
- [Slowsort](slowsort.md) - Another pedagogical bad sort
- [Merge Sort](merge_sort.md) - What to use instead

