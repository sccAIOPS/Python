# Radix Sort

## Overview

| Property | Value |
|----------|-------|
| **Category** | Sorting Algorithm (Non-Comparison-Based) |
| **Time Complexity** | O(d × (n + k)) |
| **Space Complexity** | O(n + k) |
| **Stability** | Yes |
| **In-place** | No |
| **Source File** | [`sorts/radix_sort.py`](../../../sorts/radix_sort.py), [`sorts/msd_radix_sort.py`](../../../sorts/msd_radix_sort.py) |

Where:
- $d$ = number of digits
- $n$ = number of elements
- $k$ = radix (base), typically 10

---

## 1. Mathematical Foundation

### 1.1 Problem Definition

$$
\text{Given: } A = \{a_1, a_2, \ldots, a_n\} \text{ where each } a_i \text{ is a } d\text{-digit number}
$$

$$
\text{Sort } A \text{ in } O(d \cdot n) \text{ time when } k = O(n)
$$

### 1.2 Core Mathematical Concepts

**Digit Extraction:**

For a number $x$ in base $k$ (radix):

$$
\text{digit}_i(x) = \left\lfloor \frac{x}{k^i} \right\rfloor \mod k
$$

**Number Representation:**
$$
x = \sum_{i=0}^{d-1} \text{digit}_i(x) \cdot k^i
$$

### 1.3 Key Formulas

**Number of Digits:**
$$
d = \lfloor \log_k(\max(A)) \rfloor + 1
$$

**Total Comparisons:** Zero (non-comparison sort)

**Total Time:**
$$
T(n, d, k) = d \times O(n + k) = O(d(n + k))
$$

When $k = O(n)$ and $d$ is constant: $T(n) = O(n)$

### 1.4 Why Process Least Significant Digit First (LSD)?

**Theorem:** If we use a stable sort for each digit position, processing from LSD to MSD produces correct ordering.

**Proof:** After sorting by digit position $i$, all elements are ordered by their lower $i$ digits. When we sort by position $i+1$, stability ensures elements with the same digit at position $i+1$ remain ordered by their lower $i$ digits.

---

## 2. Algorithm Description

### 2.1 Intuition

Radix Sort processes numbers digit by digit, using counting sort (or another stable sort) as a subroutine. Like sorting cards by suit, then by rank within each suit.

Two variants:
- **LSD (Least Significant Digit):** Right to left, most common
- **MSD (Most Significant Digit):** Left to right, recursive

### 2.2 Key Insights

1. **Non-Comparison:** Bypasses O(n log n) lower bound
2. **Stable Subroutine Required:** Each digit sort must be stable
3. **Fixed-Length Keys:** Works best with uniform-length representations
4. **Digit-by-Digit:** Linear scans, cache-friendly

### 2.3 Step-by-Step Process

1. Find maximum value to determine number of digits
2. For each digit position (LSD to MSD):
   - Group elements by current digit into buckets (0-9)
   - Collect buckets in order (stable)
3. Return sorted array

---

## 3. Pseudocode

### 3.1 LSD Radix Sort

```
ALGORITHM RadixSortLSD(A, radix=10)
────────────────────────────────────────
INPUT:  A - array of n non-negative integers
        radix - base for digit extraction
OUTPUT: A sorted in ascending order

1.  IF A is empty THEN RETURN A
2.  
3.  maxVal ← max(A)
4.  place ← 1  // Current digit position (10^0, 10^1, ...)
5.  
6.  WHILE place ≤ maxVal DO
7.      // Create radix buckets
8.      buckets ← array of radix empty lists
9.      
10.     // Distribute elements into buckets
11.     FOR each x in A DO
12.         digit ← (x / place) mod radix
13.         buckets[digit].append(x)
14.     END FOR
15.     
16.     // Collect from buckets back to A
17.     i ← 0
18.     FOR b ← 0 TO radix-1 DO
19.         FOR each x in buckets[b] DO
20.             A[i] ← x
21.             i ← i + 1
22.         END FOR
23.     END FOR
24.     
25.     place ← place × radix
26. END WHILE
27. 
28. RETURN A
────────────────────────────────────────
```

### 3.2 Using Counting Sort as Subroutine

```
ALGORITHM RadixSortWithCountingSort(A)
────────────────────────────────────────
1.  maxVal ← max(A)
2.  exp ← 1
3.  
4.  WHILE maxVal / exp > 0 DO
5.      CountingSortByDigit(A, exp)
6.      exp ← exp × 10
7.  END WHILE
8.  
9.  RETURN A

ALGORITHM CountingSortByDigit(A, exp)
────────────────────────────────────────
1.  n ← length(A)
2.  output ← array of n elements
3.  count ← array of 10 zeros
4.  
5.  // Count occurrences of each digit
6.  FOR i ← 0 TO n-1 DO
7.      digit ← (A[i] / exp) mod 10
8.      count[digit] ← count[digit] + 1
9.  END FOR
10. 
11. // Prefix sums
12. FOR i ← 1 TO 9 DO
13.     count[i] ← count[i] + count[i-1]
14. END FOR
15. 
16. // Build output (stable: reverse iteration)
17. FOR i ← n-1 DOWNTO 0 DO
18.     digit ← (A[i] / exp) mod 10
19.     output[count[digit] - 1] ← A[i]
20.     count[digit] ← count[digit] - 1
21. END FOR
22. 
23. // Copy back
24. FOR i ← 0 TO n-1 DO
25.     A[i] ← output[i]
26. END FOR
────────────────────────────────────────
```

---

## 4. Complexity Analysis

### 4.1 Time Complexity

| Component | Complexity |
|-----------|------------|
| Find max | O(n) |
| Each digit pass | O(n + k) |
| Number of passes | d |
| **Total** | **O(d(n + k))** |

**Special Cases:**
- If $d$ = constant and $k$ = O(n): O(n)
- For 32-bit integers with k=256: O(4n) = O(n)

### 4.2 Space Complexity

| Type | Complexity |
|------|------------|
| Buckets/Count array | O(k) |
| Output array | O(n) |
| **Total** | **O(n + k)** |

---

## 5. Visual Representation

**Input:** `[170, 45, 75, 90, 802, 24, 2, 66]`

```
Original: [170, 45, 75, 90, 802, 24, 2, 66]

Pass 1: Sort by ones digit (place = 1)
Digit extraction: 0, 5, 5, 0, 2, 4, 2, 6
Buckets:
  0: [170, 90]
  2: [802, 2]
  4: [24]
  5: [45, 75]
  6: [66]
After: [170, 90, 802, 2, 24, 45, 75, 66]

Pass 2: Sort by tens digit (place = 10)
Digit extraction: 7, 9, 0, 0, 2, 4, 7, 6
Buckets:
  0: [802, 2]
  2: [24]
  4: [45]
  6: [66]
  7: [170, 75]
  9: [90]
After: [802, 2, 24, 45, 66, 170, 75, 90]

Pass 3: Sort by hundreds digit (place = 100)
Digit extraction: 8, 0, 0, 0, 0, 1, 0, 0
Buckets:
  0: [2, 24, 45, 66, 75, 90]
  1: [170]
  8: [802]
After: [2, 24, 45, 66, 75, 90, 170, 802]

Result: [2, 24, 45, 66, 75, 90, 170, 802] ✓
```

---

## 6. Real-World Applications

### 6.1 Industry Use Cases

| Application | Why Radix Sort |
|-------------|----------------|
| **String sorting** | Fixed-length keys (e.g., phone numbers) |
| **IP address sorting** | 4 bytes = 4 digit passes with k=256 |
| **Database indexing** | Integer keys with known bounds |
| **Suffix arrays** | Large alphabets benefit from radix approach |
| **Parallel sorting** | Each bucket independent |

### 6.2 Practical Examples

#### Sorting IP Addresses

```python
def sort_ip_addresses(ips: list[str]) -> list[str]:
    """
    Sort IP addresses using radix sort.
    Each octet is a digit (k=256).
    
    >>> sort_ip_addresses(['192.168.1.1', '10.0.0.1', '172.16.0.1'])
    ['10.0.0.1', '172.16.0.1', '192.168.1.1']
    """
    def ip_to_int(ip: str) -> int:
        parts = [int(p) for p in ip.split('.')]
        return (parts[0] << 24) + (parts[1] << 16) + (parts[2] << 8) + parts[3]
    
    def int_to_ip(n: int) -> str:
        return f"{(n >> 24) & 255}.{(n >> 16) & 255}.{(n >> 8) & 255}.{n & 255}"
    
    # Convert, radix sort, convert back
    int_ips = [ip_to_int(ip) for ip in ips]
    
    # Radix sort with k=256, d=4
    for shift in [0, 8, 16, 24]:
        buckets = [[] for _ in range(256)]
        for ip in int_ips:
            digit = (ip >> shift) & 255
            buckets[digit].append(ip)
        int_ips = [ip for bucket in buckets for ip in bucket]
    
    return [int_to_ip(ip) for ip in int_ips]
```

#### Sorting Fixed-Length Strings

```python
def radix_sort_strings(strings: list[str], max_len: int) -> list[str]:
    """
    Sort fixed-length strings using MSD radix sort.
    
    >>> radix_sort_strings(['cat', 'car', 'bar', 'bat'], 3)
    ['bar', 'bat', 'car', 'cat']
    """
    # Pad strings to max_len
    padded = [s.ljust(max_len) for s in strings]
    
    # LSD radix sort on characters
    for pos in range(max_len - 1, -1, -1):
        buckets = [[] for _ in range(128)]  # ASCII
        for s in padded:
            buckets[ord(s[pos])].append(s)
        padded = [s for bucket in buckets for s in bucket]
    
    return [s.rstrip() for s in padded]
```

---

## 7. Comparison with Related Algorithms

| Algorithm | Time | Space | Stable | Best For |
|-----------|------|-------|--------|----------|
| **Radix Sort** | O(d(n+k)) | O(n+k) | ✅ | Fixed-length integers |
| Counting Sort | O(n+k) | O(n+k) | ✅ | Single digit/small range |
| Quick Sort | O(n log n) | O(log n) | ❌ | General purpose |
| Bucket Sort | O(n+k) | O(n+k) | ✅ | Uniform distribution |

### When to Choose Radix Sort

- ✅ Integer keys with limited digits
- ✅ Fixed-length strings
- ✅ When $d \cdot k < n \log n$
- ✅ Parallel processing available
- ❌ Variable-length keys
- ❌ Floating point numbers (complex)
- ❌ Small datasets

---

## 8. Variants

### 8.1 LSD vs MSD Radix Sort

| Property | LSD | MSD |
|----------|-----|-----|
| Direction | Right to left | Left to right |
| Recursion | No | Yes (for buckets) |
| Stability | Natural | Requires care |
| Short-circuit | No | Yes (single bucket) |
| Best for | Numbers | Strings |

### 8.2 Choosing the Radix

| Radix k | Digits d | Passes | Memory |
|---------|----------|--------|--------|
| 2 (binary) | 32 for int | 32 | Small |
| 10 (decimal) | ~10 for int | ~10 | Medium |
| 256 (byte) | 4 for int | 4 | Larger |
| 65536 (16-bit) | 2 for int | 2 | Large |

Trade-off: Larger radix = fewer passes but more memory.

---

## 9. References

- [Wikipedia: Radix Sort](https://en.wikipedia.org/wiki/Radix_sort)
- Cormen, T. H. et al. *Introduction to Algorithms*, Chapter 8.3
- McIlroy, P., Bostic, K., McIlroy, M. D. "Engineering Radix Sort"

---

*Last Updated: December 2024*
*Implementation: [sorts/radix_sort.py](../../../sorts/radix_sort.py)*
