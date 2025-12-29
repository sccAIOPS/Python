# Knuth-Morris-Pratt (KMP) Algorithm

## Overview

| Property | Value |
|----------|-------|
| **Category** | String Pattern Matching |
| **Preprocessing** | O(m) |
| **Search Time** | O(n) |
| **Total Time** | O(n + m) |
| **Space** | O(m) |
| **Source** | [knuth_morris_pratt.py](../../../strings/knuth_morris_pratt.py) |

## 1. Mathematical Foundation

### 1.1 Problem Definition

Given:
- **Text** $T[0..n-1]$ of length $n$
- **Pattern** $P[0..m-1]$ of length $m$

Find all occurrences of $P$ in $T$.

### 1.2 Failure Function (Prefix Function)

The **failure function** $\pi[i]$ for pattern $P$ is defined as:

$$\pi[i] = \max\{k : k < i \text{ and } P[0..k-1] = P[i-k+1..i]\}$$

This represents the length of the longest proper prefix of $P[0..i]$ that is also a suffix.

### 1.3 Properties of Prefix Function

1. $\pi[0] = 0$ (no proper prefix for single character)
2. $\pi[i] < i$ for all $i$
3. If $\pi[i] > 0$, then $\pi[i] - 1$ is a candidate for $\pi[i-1]$

### 1.4 Key Insight

When a mismatch occurs at position $j$ in pattern:
- Characters $P[0..j-1]$ matched
- Instead of restarting, use $\pi[j-1]$ to skip comparisons
- The prefix $P[0..\pi[j-1]-1]$ is already matched

$$\text{New position in pattern} = \pi[j-1]$$

## 2. Algorithm Pseudocode

### 2.1 Compute Prefix Function

```
ALGORITHM ComputePrefixFunction(pattern)
    INPUT: Pattern P of length m
    OUTPUT: Prefix function array π
    
    1. m ← length(P)
    2. π ← array[0..m-1] initialized to 0
    3. k ← 0  // Length of previous longest prefix suffix
    
    4. for i ← 1 to m - 1 do
           // Find longest prefix that is also suffix
           while k > 0 AND P[k] ≠ P[i] do
               k ← π[k - 1]
           end while
           
           if P[k] = P[i] then
               k ← k + 1
           end if
           
           π[i] ← k
       end for
    
    5. return π
```

### 2.2 KMP Search

```
ALGORITHM KMPSearch(text, pattern)
    INPUT: Text T of length n, Pattern P of length m
    OUTPUT: List of starting positions where P occurs in T
    
    1. n ← length(T)
    2. m ← length(P)
    3. π ← ComputePrefixFunction(P)
    4. matches ← []
    5. j ← 0  // Index in pattern
    
    6. for i ← 0 to n - 1 do
           // Handle mismatch
           while j > 0 AND P[j] ≠ T[i] do
               j ← π[j - 1]
           end while
           
           // Match current character
           if P[j] = T[i] then
               j ← j + 1
           end if
           
           // Complete match found
           if j = m then
               matches.append(i - m + 1)
               j ← π[j - 1]  // Continue searching
           end if
       end for
    
    7. return matches
```

## 3. Complexity Analysis

### 3.1 Time Complexity

**Preprocessing (Prefix Function):**
- Each character examined at most twice
- $O(m)$ total

**Search Phase:**
- Each text character examined once
- $j$ can increase at most $n$ times
- $j$ decreases through $\pi$ at most $n$ times (amortized)
- $O(n)$ total

**Total: $O(n + m)$**

### 3.2 Space Complexity

- Prefix function array: $O(m)$
- Other variables: $O(1)$
- **Total: $O(m)$**

## 4. Visual Representation

### 4.1 Prefix Function Example

```
Pattern: A B A B A C A
Index:   0 1 2 3 4 5 6

Computing π:
i=0: π[0] = 0 (by definition)

i=1: Compare P[0]=A with P[1]=B → mismatch
     π[1] = 0

i=2: Compare P[0]=A with P[2]=A → match
     π[2] = 1

i=3: Compare P[1]=B with P[3]=B → match  
     π[3] = 2

i=4: Compare P[2]=A with P[4]=A → match
     π[4] = 3

i=5: Compare P[3]=B with P[5]=C → mismatch
     k = π[2] = 1
     Compare P[1]=B with P[5]=C → mismatch
     k = π[0] = 0
     Compare P[0]=A with P[5]=C → mismatch
     π[5] = 0

i=6: Compare P[0]=A with P[6]=A → match
     π[6] = 1

Result: π = [0, 0, 1, 2, 3, 0, 1]
```

### 4.2 Search Example

```
Text:    A B A B A B A C A B A
Pattern: A B A B A C A
π:       [0, 0, 1, 2, 3, 0, 1]

Step-by-step:
i=0: T[0]=A, P[0]=A → match, j=1
i=1: T[1]=B, P[1]=B → match, j=2
i=2: T[2]=A, P[2]=A → match, j=3
i=3: T[3]=B, P[3]=B → match, j=4
i=4: T[4]=A, P[4]=A → match, j=5
i=5: T[5]=B, P[5]=C → mismatch!
     j = π[4] = 3
     T[5]=B, P[3]=B → match, j=4
i=6: T[6]=A, P[4]=A → match, j=5
i=7: T[7]=C, P[5]=C → match, j=6
i=8: T[8]=A, P[6]=A → match, j=7 = m
     MATCH at position 2!
     j = π[6] = 1

Continue searching...
```

```mermaid
flowchart TD
    subgraph "KMP Search Flow"
        A[Start at i=0, j=0] --> B{P[j] = T[i]?}
        B -->|Yes| C[j++, i++]
        B -->|No| D{j > 0?}
        D -->|Yes| E[j = π[j-1]]
        D -->|No| F[i++]
        E --> B
        C --> G{j = m?}
        G -->|Yes| H[Match found at i-m]
        G -->|No| I{i < n?}
        H --> J[j = π[j-1]]
        J --> I
        F --> I
        I -->|Yes| B
        I -->|No| K[End]
    end
```

## 5. Real-World Software Engineering Applications

### 5.1 Industry Use Cases

1. **Text Editors**
   - Find and replace functionality
   - Syntax highlighting
   - Code navigation

2. **Search Engines**
   - Substring matching
   - Query processing
   - Document indexing

3. **Bioinformatics**
   - DNA sequence matching
   - Protein pattern finding
   - Genome analysis

4. **Network Security**
   - Intrusion detection systems
   - Pattern-based filtering
   - Deep packet inspection

5. **Compilers**
   - Lexical analysis
   - Token recognition
   - Keyword detection

### 5.2 Implementation Examples

```python
from typing import List


def compute_prefix_function(pattern: str) -> List[int]:
    """
    Compute the prefix function (failure function) for KMP algorithm.
    
    The prefix function π[i] is the length of the longest proper prefix
    of pattern[0..i] that is also a suffix.
    
    >>> compute_prefix_function("ABABACA")
    [0, 0, 1, 2, 3, 0, 1]
    >>> compute_prefix_function("AAAA")
    [0, 1, 2, 3]
    >>> compute_prefix_function("ABCD")
    [0, 0, 0, 0]
    >>> compute_prefix_function("AABAAB")
    [0, 1, 0, 1, 2, 3]
    """
    m = len(pattern)
    pi = [0] * m
    k = 0  # Length of previous longest prefix suffix
    
    for i in range(1, m):
        # Fall back through prefix function
        while k > 0 and pattern[k] != pattern[i]:
            k = pi[k - 1]
        
        # Extend if current character matches
        if pattern[k] == pattern[i]:
            k += 1
        
        pi[i] = k
    
    return pi


def kmp_search(text: str, pattern: str) -> List[int]:
    """
    Find all occurrences of pattern in text using KMP algorithm.
    
    Time: O(n + m) where n = len(text), m = len(pattern)
    Space: O(m) for prefix function
    
    >>> kmp_search("ABABDABACDABABCABAB", "ABABCABAB")
    [10]
    >>> kmp_search("AAAAAA", "AAA")
    [0, 1, 2, 3]
    >>> kmp_search("ABC", "D")
    []
    >>> kmp_search("ABCABC", "ABC")
    [0, 3]
    """
    if not pattern:
        return list(range(len(text) + 1))
    if not text or len(pattern) > len(text):
        return []
    
    n, m = len(text), len(pattern)
    pi = compute_prefix_function(pattern)
    matches = []
    j = 0  # Index in pattern
    
    for i in range(n):
        # Handle mismatch by using prefix function
        while j > 0 and pattern[j] != text[i]:
            j = pi[j - 1]
        
        # Current characters match
        if pattern[j] == text[i]:
            j += 1
        
        # Complete match found
        if j == m:
            matches.append(i - m + 1)
            j = pi[j - 1]  # Continue searching for overlapping matches
    
    return matches


def kmp_search_first(text: str, pattern: str) -> int:
    """
    Find first occurrence of pattern in text.
    
    >>> kmp_search_first("Hello World", "World")
    6
    >>> kmp_search_first("Hello World", "xyz")
    -1
    >>> kmp_search_first("AAAA", "AA")
    0
    """
    if not pattern:
        return 0
    if not text or len(pattern) > len(text):
        return -1
    
    n, m = len(text), len(pattern)
    pi = compute_prefix_function(pattern)
    j = 0
    
    for i in range(n):
        while j > 0 and pattern[j] != text[i]:
            j = pi[j - 1]
        
        if pattern[j] == text[i]:
            j += 1
        
        if j == m:
            return i - m + 1
    
    return -1


def count_occurrences(text: str, pattern: str) -> int:
    """
    Count non-overlapping occurrences of pattern.
    
    >>> count_occurrences("AAAA", "AA")
    2
    >>> count_occurrences("ABCABC", "ABC")
    2
    """
    return len(kmp_search(text, pattern))


def count_overlapping(text: str, pattern: str) -> int:
    """
    Count overlapping occurrences of pattern.
    
    >>> count_overlapping("AAAA", "AA")
    3
    >>> count_overlapping("ABCABC", "ABC")
    2
    """
    if not pattern:
        return len(text) + 1
    
    n, m = len(text), len(pattern)
    pi = compute_prefix_function(pattern)
    count = 0
    j = 0
    
    for i in range(n):
        while j > 0 and pattern[j] != text[i]:
            j = pi[j - 1]
        
        if pattern[j] == text[i]:
            j += 1
        
        if j == m:
            count += 1
            j = pi[j - 1]
    
    return count


class KMPMatcher:
    """
    Reusable KMP matcher with precomputed prefix function.
    
    >>> matcher = KMPMatcher("ABC")
    >>> matcher.find_all("XYZABCABCDEF")
    [3, 6]
    >>> matcher.find_first("DEFABCXYZ")
    3
    """
    
    def __init__(self, pattern: str):
        self.pattern = pattern
        self.m = len(pattern)
        self.pi = compute_prefix_function(pattern) if pattern else []
    
    def find_all(self, text: str) -> List[int]:
        """Find all occurrences in text."""
        return kmp_search(text, self.pattern)
    
    def find_first(self, text: str) -> int:
        """Find first occurrence in text."""
        return kmp_search_first(text, self.pattern)
    
    def contains(self, text: str) -> bool:
        """Check if pattern exists in text."""
        return self.find_first(text) != -1
    
    def count(self, text: str, overlapping: bool = True) -> int:
        """Count occurrences."""
        if overlapping:
            return count_overlapping(text, self.pattern)
        return count_occurrences(text, self.pattern)


# Applications

def find_pattern_in_file(filepath: str, pattern: str) -> List[tuple]:
    """
    Find all occurrences of pattern in a file.
    Returns list of (line_number, column, line_text).
    
    >>> # Example usage with in-memory text
    >>> text = "Line 1 has ABC\\nLine 2 has ABC too"
    >>> lines = text.split('\\n')
    >>> results = []
    >>> for i, line in enumerate(lines):
    ...     for pos in kmp_search(line, "ABC"):
    ...         results.append((i+1, pos+1, line))
    >>> len(results)
    2
    """
    results = []
    matcher = KMPMatcher(pattern)
    
    with open(filepath, 'r') as f:
        for line_num, line in enumerate(f, 1):
            positions = matcher.find_all(line)
            for pos in positions:
                results.append((line_num, pos + 1, line.rstrip()))
    
    return results


def highlight_matches(text: str, pattern: str, 
                      start_tag: str = "**", end_tag: str = "**") -> str:
    """
    Return text with pattern occurrences highlighted.
    
    >>> highlight_matches("ABC in XABCX", "ABC")
    '**ABC** in X**ABC**X'
    """
    if not pattern:
        return text
    
    positions = kmp_search(text, pattern)
    if not positions:
        return text
    
    result = []
    prev_end = 0
    
    for pos in positions:
        # Add text before match
        result.append(text[prev_end:pos])
        # Add highlighted match
        result.append(start_tag)
        result.append(text[pos:pos + len(pattern)])
        result.append(end_tag)
        prev_end = pos + len(pattern)
    
    # Add remaining text
    result.append(text[prev_end:])
    
    return ''.join(result)


def longest_repeated_substring(text: str) -> str:
    """
    Find longest substring that appears at least twice.
    Uses KMP prefix function property.
    
    >>> longest_repeated_substring("ABCABCABC")
    'ABCABC'
    >>> longest_repeated_substring("ABCD")
    ''
    """
    if len(text) < 2:
        return ""
    
    # For each position, find longest repeated substring ending there
    best = ""
    
    for length in range(len(text) - 1, 0, -1):
        pattern = text[:length]
        # Check if pattern appears again
        if kmp_search_first(text[1:], pattern) != -1:
            return pattern
    
    return best


def is_rotation(s1: str, s2: str) -> bool:
    """
    Check if s2 is a rotation of s1 using KMP.
    
    >>> is_rotation("ABCD", "CDAB")
    True
    >>> is_rotation("ABCD", "ACBD")
    False
    >>> is_rotation("AAA", "AAA")
    True
    """
    if len(s1) != len(s2):
        return False
    if not s1:
        return True
    
    # s2 is rotation of s1 iff s2 is substring of s1+s1
    doubled = s1 + s1
    return kmp_search_first(doubled, s2) != -1
```

## 6. Variations and Extensions

### 6.1 Multiple Pattern KMP

For searching multiple patterns, consider using **Aho-Corasick** algorithm instead.

### 6.2 2D Pattern Matching

KMP can be extended for 2D pattern matching in matrices.

### 6.3 Approximate Matching

Combine with edit distance for fuzzy matching.

## 7. Comparison with Other Algorithms

| Algorithm | Best Case | Average | Worst Case | Space | Use When |
|-----------|-----------|---------|------------|-------|----------|
| **KMP** | O(n) | O(n+m) | O(n+m) | O(m) | Single pattern, guaranteed performance |
| **Boyer-Moore** | O(n/m) | O(n+m) | O(nm) | O(m+σ) | Long patterns, large alphabets |
| **Rabin-Karp** | O(n) | O(n+m) | O(nm) | O(1) | Multiple pattern search |
| **Naive** | O(n) | O(nm) | O(nm) | O(1) | Short patterns, simple cases |

## 8. References

- Knuth, D. E., Morris, J. H., & Pratt, V. R. (1977). "Fast Pattern Matching in Strings"
- Cormen, T. H. et al. "Introduction to Algorithms" - Chapter 32
- [Wikipedia: Knuth–Morris–Pratt algorithm](https://en.wikipedia.org/wiki/Knuth%E2%80%93Morris%E2%80%93Pratt_algorithm)
