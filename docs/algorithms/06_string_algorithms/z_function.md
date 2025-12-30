# Z-Function Algorithm

## Overview
- **Category**: String Pattern Matching / String Processing
- **Complexity**: Time: O(n) | Space: O(n)
- **Type**: Linear-time string matching
- **Source File**: [strings/z_function.py](../../../strings/z_function.py)

## 1. Mathematical Foundation

### 1.1 Problem Definition

For a string $S[0..n-1]$, the Z-function produces an array $Z[0..n-1]$ where:

$$
Z[i] = \text{length of longest substring starting at } i \text{ that matches a prefix of } S
$$

Formally:
$$
Z[i] = \max\{k : S[0..k-1] = S[i..i+k-1]\}
$$

### 1.2 Z-Box Concept

A **Z-box** at position $i$ is the interval $[i, i + Z[i] - 1]$ representing the match.

We maintain a window $[l, r]$ representing the rightmost Z-box found so far:
$$
r = \max_{j < i}\{j + Z[j] - 1\}
$$

### 1.3 Recurrence Relation

For position $i$:
$$
Z[i] = \begin{cases}
0 & \text{if } i > r \\
\min(Z[i-l], r-i+1) & \text{if } i \leq r \text{ (initial estimate)}
\end{cases}
$$

Then extend by direct comparison while $S[Z[i]] = S[i + Z[i]]$.

## 2. Algorithm Description

### 2.1 Intuition

The Z-function cleverly reuses previously computed information:
1. If current position $i$ is within a known Z-box, we have partial information
2. The value at $i - l$ in the Z-array tells us about the prefix match
3. We only need to verify/extend beyond what we already know

### 2.2 Key Insight

The position $i - l$ relative to the current Z-box mirrors position $0$ in the original string. This symmetry allows us to skip redundant comparisons.

## 3. Pseudocode

```
ALGORITHM Z-Function(S)
    INPUT: String S of length n
    OUTPUT: Z-array where Z[i] = length of longest prefix match at i
    
    n ← length(S)
    Z ← array of size n, initialized to 0
    Z[0] ← n  // or 0 by convention
    
    l ← 0, r ← 0  // Z-box boundaries
    
    for i ← 1 to n-1 do
        if i > r then
            // Outside Z-box: compute from scratch
            l ← i
            r ← i
            while r < n AND S[r - l] = S[r] do
                r ← r + 1
            Z[i] ← r - l
            r ← r - 1
        else
            // Inside Z-box: use previous computation
            k ← i - l
            
            if Z[k] < r - i + 1 then
                // Z-box at k fits inside current Z-box
                Z[i] ← Z[k]
            else
                // Need to extend beyond current Z-box
                l ← i
                while r < n AND S[r - l] = S[r] do
                    r ← r + 1
                Z[i] ← r - l
                r ← r - 1
    
    return Z
```

## 4. Pattern Matching Using Z-Function

```
ALGORITHM Z-Search(text, pattern)
    INPUT: Text T of length n, Pattern P of length m
    OUTPUT: All starting positions of P in T
    
    // Concatenate with separator
    S ← P + "$" + T
    Z ← Z-Function(S)
    
    matches ← empty list
    for i ← m + 1 to length(S) - 1 do
        if Z[i] = m then
            matches.add(i - m - 1)
    
    return matches
```

## 5. Complexity Analysis

### 5.1 Time Complexity

| Operation | Complexity | Explanation |
|-----------|------------|-------------|
| Z-function | O(n) | Each position compared at most twice |
| Pattern match | O(n + m) | Build Z-array for concatenated string |

**Proof of Linearity:**
- Each comparison either increments $r$ or uses precomputed value
- $r$ can only increase from 0 to n-1
- Total comparisons ≤ 2n

### 5.2 Space Complexity
- Z-array: O(n)
- For pattern matching: O(n + m)

## 6. Visual Representation

### 6.1 Example: S = "aabxaab"

```
Index:  0 1 2 3 4 5 6
String: a a b x a a b
Z:      7 1 0 0 3 1 0

Position 0: Z[0] = 7 (entire string matches itself)
Position 1: "ab..." vs "aabx...", Z[1] = 1 (only 'a' matches)
Position 2: "bx..." vs "aab...", Z[2] = 0 (no match)
Position 3: "x..." vs "aab...", Z[3] = 0 (no match)
Position 4: "aab" vs "aab...", Z[4] = 3 (matches "aab")
Position 5: "ab" vs "aab...", Z[5] = 1 (only 'a' matches)
Position 6: "b" vs "aab...", Z[6] = 0 (no match)
```

### 6.2 Z-Box Visualization

```
String: a a b x a a b
        └─────────────┘ Z[0] = 7
          └┘            Z[1] = 1
            ╳           Z[2] = 0
              ╳         Z[3] = 0
                └───┘   Z[4] = 3 (Z-box: [4, 6])
                  └┘    Z[5] = 1
                    ╳   Z[6] = 0
```

### 6.3 Pattern Matching Example

```
Pattern: "ab"
Text:    "xababab"
Concatenated: "ab$xababab"

Index:  0 1 2 3 4 5 6 7 8 9
String: a b $ x a b a b a b
Z:      - 0 0 0 2 0 2 0 2 0

Matches at Z[i] = 2: positions 4, 6, 8
Original text positions: 4-3=1, 6-3=3, 8-3=5
Pattern found at indices: 1, 3, 5 in text
```

```mermaid
flowchart TD
    A[Start i=1, l=0, r=0] --> B{i > r?}
    B -->|Yes| C[Compute from scratch]
    B -->|No| D[Use Z-box info]
    C --> E[l=i, extend r while match]
    D --> F{Z[i-l] < r-i+1?}
    F -->|Yes| G[Z[i] = Z[i-l]]
    F -->|No| H[l=i, extend r]
    E --> I[Z[i] = r-l, r--]
    G --> J{i < n?}
    H --> I
    I --> J
    J -->|Yes| K[i++]
    K --> B
    J -->|No| L[Return Z]
```

## 7. Applications Beyond Pattern Matching

### 7.1 Counting Distinct Substrings

```python
def count_distinct_substrings(s: str) -> int:
    """Count distinct substrings using Z-function."""
    n = len(s)
    count = 0
    
    for i in range(n):
        # Process suffix starting at i
        suffix = s[i:]
        z = z_function(suffix)
        
        # New substrings = length - max Z value
        max_z = max(z[1:]) if len(z) > 1 else 0
        count += len(suffix) - max_z
    
    return count
```

### 7.2 String Compression

Find the shortest period $p$ such that $S$ is a repetition of $S[0..p-1]$:

```python
def minimum_period(s: str) -> int:
    """Find minimum period using Z-function."""
    n = len(s)
    z = z_function(s)
    
    for i in range(1, n):
        if i + z[i] == n and n % i == 0:
            return i
    return n
```

### 7.3 Longest Palindromic Prefix

```python
def longest_palindrome_prefix(s: str) -> int:
    """Find longest prefix that is a palindrome."""
    t = s + "$" + s[::-1]
    z = z_function(t)
    
    n = len(s)
    for i in range(n + 1, 2 * n + 1):
        if i + z[i] == 2 * n + 1:
            return 2 * n + 1 - i
    return 0
```

## 8. Comparison with Related Algorithms

| Algorithm | Time | Space | Strength |
|-----------|------|-------|----------|
| **Z-function** | O(n) | O(n) | Simple, versatile |
| KMP failure function | O(n) | O(n) | Streaming friendly |
| Suffix array | O(n log n) | O(n) | Multiple queries |
| Suffix tree | O(n) | O(n) | Complex queries |

### 8.1 Z-function vs KMP Failure Function

Both are linear and related:
- Z-function: prefix matching at each position
- KMP failure: longest proper border at each position

Conversion between them is possible in O(n).

## 9. Real-World Software Engineering Applications

### 9.1 Industry Use Cases

1. **Text Processing**
   - Find all occurrences of substring
   - Auto-complete suggestions
   - Spell checking

2. **Bioinformatics**
   - Tandem repeat detection in DNA
   - Finding repeated motifs
   - Genome annotation

3. **Data Compression**
   - Finding repeated patterns for LZ-family algorithms
   - Dictionary-based compression
   - String deduplication

4. **Version Control (diff algorithms)**
   - Finding common prefixes between versions
   - Patch generation

### 9.2 Production Example

```python
class TextAnalyzer:
    """Text analysis using Z-function."""
    
    @staticmethod
    def z_function(s: str) -> list[int]:
        """Compute Z-function in O(n) time."""
        n = len(s)
        z = [0] * n
        z[0] = n
        l, r = 0, 0
        
        for i in range(1, n):
            if i > r:
                l, r = i, i
                while r < n and s[r - l] == s[r]:
                    r += 1
                z[i] = r - l
                r -= 1
            else:
                k = i - l
                if z[k] < r - i + 1:
                    z[i] = z[k]
                else:
                    l = i
                    while r < n and s[r - l] == s[r]:
                        r += 1
                    z[i] = r - l
                    r -= 1
        
        return z
    
    def find_all_occurrences(self, text: str, pattern: str) -> list[int]:
        """Find all pattern occurrences in text."""
        if not pattern or not text:
            return []
        
        combined = pattern + "$" + text
        z = self.z_function(combined)
        
        m = len(pattern)
        return [i - m - 1 for i in range(m + 1, len(combined)) if z[i] == m]
    
    def find_repetitions(self, s: str) -> list[tuple[int, int, int]]:
        """
        Find all tandem repetitions in string.
        Returns list of (start, period, count).
        """
        n = len(s)
        z = self.z_function(s)
        repetitions = []
        
        for period in range(1, n // 2 + 1):
            i = period
            while i < n:
                if z[i] >= period:
                    count = z[i] // period + 1
                    repetitions.append((i - period, period, count))
                    i += z[i]
                else:
                    i += 1
        
        return repetitions
```

### 9.3 Use in Competitive Programming

The Z-function is a favorite in competitive programming for:
- String matching in O(n + m)
- Finding periods and borders
- Counting substring occurrences
- Palindrome-related problems

## 10. Edge Cases

| Edge Case | Z-value | Notes |
|-----------|---------|-------|
| Empty string | [] | Return empty array |
| Single char | [1] | Trivial case |
| All same chars | [n, n-1, ..., 1] | Decreasing sequence |
| No repetition | [n, 0, 0, ..., 0] | Only position 0 has match |

## 11. References

- [CP-Algorithms: Z-function](https://cp-algorithms.com/string/z-function.html)
- Gusfield, D. (1997). "Algorithms on Strings, Trees and Sequences"
- [Wikipedia: Z-algorithm](https://en.wikipedia.org/wiki/Z_algorithm)
