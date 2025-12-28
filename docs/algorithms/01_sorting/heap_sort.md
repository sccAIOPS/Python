# Heap Sort

## Overview

| Property | Value |
|----------|-------|
| **Category** | Sorting Algorithm (Comparison-Based) |
| **Time Complexity (Best)** | O(n log n) |
| **Time Complexity (Average)** | O(n log n) |
| **Time Complexity (Worst)** | O(n log n) |
| **Space Complexity** | O(1) |
| **Stability** | No |
| **In-place** | Yes |
| **Source File** | [`sorts/heap_sort.py`](../../../sorts/heap_sort.py) |

---

## 1. Mathematical Foundation

### 1.1 Problem Definition

$$
\text{Given: } A = \{a_1, a_2, \ldots, a_n\}
$$

$$
\text{Find: } A' \text{ such that } a'_1 \leq a'_2 \leq \ldots \leq a'_n
$$

### 1.2 Core Mathematical Concepts

**Binary Heap Property:**

A binary heap is a complete binary tree satisfying the heap property:

**Max-Heap:** For every node $i$ other than the root:
$$
A[\text{parent}(i)] \geq A[i]
$$

**Array Representation of Complete Binary Tree:**

For node at index $i$ (0-based):
$$
\text{parent}(i) = \lfloor (i-1)/2 \rfloor
$$
$$
\text{left}(i) = 2i + 1
$$
$$
\text{right}(i) = 2i + 2
$$

**Height of Heap:**
$$
h = \lfloor \log_2 n \rfloor
$$

### 1.3 Key Formulas

**Heapify Time Complexity:**
$$
T_{\text{heapify}}(h) = O(h) = O(\log n)
$$

**Build Heap Time Complexity:**

Sum over all non-leaf nodes:
$$
T_{\text{build}} = \sum_{h=0}^{\lfloor \log n \rfloor} \lceil \frac{n}{2^{h+1}} \rceil \cdot O(h)
$$
$$
= O\left(n \sum_{h=0}^{\lfloor \log n \rfloor} \frac{h}{2^h}\right) = O(n)
$$

**Total Heap Sort Time:**
$$
T(n) = O(n) + n \cdot O(\log n) = O(n \log n)
$$

### 1.4 Proof of Correctness

**Heapify Correctness:**

*Loop Invariant:* Before each call to heapify(A, i), the trees rooted at left(i) and right(i) are max-heaps.

**Heap Sort Correctness:**

*Invariant:* At the start of each iteration of the extraction loop:
1. $A[0..i]$ is a max-heap containing the $i+1$ smallest elements
2. $A[i+1..n-1]$ contains the $n-i-1$ largest elements in sorted order

---

## 2. Algorithm Description

### 2.1 Intuition

Heap Sort uses a binary heap data structure. The algorithm has two phases:
1. **Build Max-Heap:** Convert array into a max-heap (largest element at root)
2. **Extract Maximum:** Repeatedly swap root with last element, reduce heap size, and heapify

Think of it as repeatedly finding the maximum element and placing it at the end.

### 2.2 Key Insights

1. **Guaranteed O(n log n):** No bad cases unlike Quick Sort
2. **In-Place:** Uses only O(1) extra space
3. **Not Cache-Friendly:** Poor locality of reference compared to Quick Sort
4. **Heap Structure:** Implicit in array indices, no pointers needed

### 2.3 Step-by-Step Process

1. **Build Max-Heap:** Bottom-up heapify from last non-leaf to root
2. **For i from n-1 downto 1:**
   - Swap A[0] (max) with A[i]
   - Reduce heap size by 1
   - Heapify the root

---

## 3. Pseudocode

### 3.1 Heapify Procedure

```
ALGORITHM Heapify(A, i, heapSize)
────────────────────────────────────────
INPUT:  A - array representing heap
        i - index to heapify
        heapSize - current heap size
OUTPUT: A with max-heap property at i

1.  largest ← i
2.  left ← 2*i + 1
3.  right ← 2*i + 2
4.  
5.  IF left < heapSize AND A[left] > A[largest] THEN
6.      largest ← left
7.  END IF
8.  
9.  IF right < heapSize AND A[right] > A[largest] THEN
10.     largest ← right
11. END IF
12. 
13. IF largest ≠ i THEN
14.     SWAP(A[i], A[largest])
15.     Heapify(A, largest, heapSize)
16. END IF
────────────────────────────────────────
```

### 3.2 Heap Sort Algorithm

```
ALGORITHM HeapSort(A)
────────────────────────────────────────
INPUT:  A - array of n elements
OUTPUT: A sorted in ascending order

1.  n ← length(A)
2.  
3.  // Build max-heap (bottom-up)
4.  FOR i ← n/2 - 1 DOWNTO 0 DO
5.      Heapify(A, i, n)
6.  END FOR
7.  
8.  // Extract elements from heap
9.  FOR i ← n - 1 DOWNTO 1 DO
10.     SWAP(A[0], A[i])
11.     Heapify(A, 0, i)
12. END FOR
13. 
14. RETURN A
────────────────────────────────────────
```

---

## 4. Complexity Analysis

### 4.1 Time Complexity

| Case | Complexity | Condition |
|------|------------|-----------|
| **Best** | O(n log n) | All cases |
| **Average** | O(n log n) | All cases |
| **Worst** | O(n log n) | All cases |

**Detailed Analysis:**

```
Build Heap: O(n)
- More nodes are at lower levels with less work
- Mathematical proof shows O(n) total

Extraction Phase: O(n log n)
- n-1 extractions
- Each extraction: O(log n) heapify
- Total: O(n log n)

Overall: O(n) + O(n log n) = O(n log n)
```

### 4.2 Space Complexity

| Type | Complexity | Description |
|------|------------|-------------|
| **Auxiliary Space** | O(1) | In-place algorithm |
| **Recursion Stack** | O(log n) | Heapify depth |
| **Total** | O(1) iterative, O(log n) recursive |

---

## 5. Visual Representation

### 5.1 Build Heap Example

**Input:** `[4, 10, 3, 5, 1]`

```
Initial Array: [4, 10, 3, 5, 1]

As tree:        4
              /   \
            10     3
           /  \
          5    1

After heapify(1): [4, 10, 3, 5, 1] (no change, 10 > 5, 1)

After heapify(0):
                 10
               /    \
              5      3
             / \
            4   1

Max-Heap: [10, 5, 3, 4, 1]
```

### 5.2 Extraction Phase

```
Heap: [10, 5, 3, 4, 1]

Step 1: Swap(10, 1), Heapify
        [1, 5, 3, 4] | [10]
        → [5, 4, 3, 1] | [10]

Step 2: Swap(5, 1), Heapify
        [1, 4, 3] | [5, 10]
        → [4, 1, 3] | [5, 10]

Step 3: Swap(4, 3), Heapify
        [3, 1] | [4, 5, 10]

Step 4: Swap(3, 1)
        [1] | [3, 4, 5, 10]

Result: [1, 3, 4, 5, 10]
```

---

## 6. Real-World Applications

### 6.1 Industry Use Cases

| Industry | Application | Why Heap Sort |
|----------|-------------|---------------|
| **Embedded Systems** | Memory-constrained sorting | O(1) extra space |
| **Operating Systems** | Priority scheduling | Heap structure natural fit |
| **Real-time Systems** | Predictable performance | No O(n²) worst case |

### 6.2 Practical Examples

#### Priority Queue Implementation

```python
class PriorityQueue:
    """
    Priority queue using heap sort principles.
    Used in: Dijkstra's algorithm, task scheduling.
    """
    def __init__(self):
        self.heap = []
    
    def push(self, item: int) -> None:
        self.heap.append(item)
        self._sift_up(len(self.heap) - 1)
    
    def pop(self) -> int:
        if not self.heap:
            raise IndexError("empty queue")
        result = self.heap[0]
        last = self.heap.pop()
        if self.heap:
            self.heap[0] = last
            self._sift_down(0)
        return result
```

### 6.3 Libraries Using Heap Sort

| Library | Language | Usage |
|---------|----------|-------|
| **heapq** | Python | Priority queue operations |
| **std::make_heap** | C++ | Heap operations |
| **PriorityQueue** | Java | Task scheduling |

---

## 7. Comparison with Related Algorithms

| Algorithm | Time (Worst) | Space | Stable | Cache |
|-----------|--------------|-------|--------|-------|
| **Heap Sort** | O(n log n) | O(1) | ❌ | ⭐ |
| Quick Sort | O(n²) | O(log n) | ❌ | ⭐⭐⭐ |
| Merge Sort | O(n log n) | O(n) | ✅ | ⭐⭐ |

### When to Choose Heap Sort

- ✅ Memory constraints (O(1) space)
- ✅ Guaranteed O(n log n) needed
- ✅ Building priority queues
- ❌ Cache performance matters
- ❌ Stability required

---

## 8. References

- Williams, J.W.J. (1964). "Algorithm 232 - Heapsort". *Communications of the ACM*
- Floyd, R.W. (1964). "Algorithm 245 - Treesort 3". *Communications of the ACM*
- [Wikipedia: Heapsort](https://en.wikipedia.org/wiki/Heapsort)

---

*Last Updated: December 2024*
*Implementation: [sorts/heap_sort.py](../../../sorts/heap_sort.py)*
