# Manacher's Algorithm

## Overview
- **Category**: String Processing / Palindrome Detection
- **Complexity**: Time: O(n) | Space: O(n)
- **Type**: Longest palindromic substring
- **Source File**: [strings/manacher.py](../../../strings/manacher.py)

## 1. Mathematical Foundation

### 1.1 Problem Definition

Given a string $S[0..n-1]$, find the longest palindromic substring.

A palindrome centered at position $c$ with radius $r$ satisfies:
$$
S[c-i] = S[c+i] \quad \forall i \in [0, r]
$$

### 1.2 Transformation

To handle both odd and even length palindromes uniformly, transform $S$ by inserting separators:

$$
T = \#S[0]\#S[1]\#...\#S[n-1]\#
$$

Example: "abba" → "#a#b#b#a#"

### 1.3 Key Insight

For the transformed string $T$:
- Every palindrome in $T$ has odd length
- If palindrome radius in $T$ is $r$, original length is $r$
- Center at position $i$ in $T$ corresponds to position $\lfloor i/2 \rfloor$ in $S$

### 1.4 Mirror Property

If we have a palindrome centered at $c$ extending to right boundary $R$:
$$
P[i] \geq \min(P[2c - i], R - i) \quad \text{for } i < R
$$

Where $P[i]$ is the palindrome radius at position $i$, and $2c - i$ is the mirror of $i$ around $c$.

## 2. Algorithm Description

### 2.1 Intuition

Manacher's algorithm exploits palindrome symmetry:
1. Maintain the rightmost palindrome boundary seen so far
2. Use mirror property to initialize radius at new positions
3. Only expand beyond known boundaries

### 2.2 Three Cases

When computing $P[i]$ with current center $c$ and right boundary $R$:

1. **Case 1**: $i \geq R$ → Compute from scratch
2. **Case 2**: $i + P[mirror] < R$ → $P[i] = P[mirror]$ (mirror fits inside)
3. **Case 3**: $i + P[mirror] \geq R$ → $P[i] \geq R - i$, then expand

## 3. Pseudocode

```
ALGORITHM Manacher(S)
    INPUT: String S of length n
    OUTPUT: Longest palindromic substring
    
    // Transform string
    T ← "#"
    for each char c in S do
        T ← T + c + "#"
    
    n ← length(T)
    P ← array of size n, initialized to 0
    
    C ← 0  // Center of rightmost palindrome
    R ← 0  // Right boundary of rightmost palindrome
    
    for i ← 0 to n-1 do
        // Mirror position
        mirror ← 2*C - i
        
        // Initialize P[i] using mirror property
        if i < R then
            P[i] ← min(R - i, P[mirror])
        
        // Expand palindrome centered at i
        while i + P[i] + 1 < n AND i - P[i] - 1 ≥ 0 
              AND T[i + P[i] + 1] = T[i - P[i] - 1] do
            P[i] ← P[i] + 1
        
        // Update center and right boundary
        if i + P[i] > R then
            C ← i
            R ← i + P[i]
    
    // Find maximum palindrome
    maxLen ← 0
    centerIndex ← 0
    for i ← 0 to n-1 do
        if P[i] > maxLen then
            maxLen ← P[i]
            centerIndex ← i
    
    // Extract original substring
    start ← (centerIndex - maxLen) / 2
    return S[start : start + maxLen]
```

## 4. Complexity Analysis

### 4.1 Time Complexity

**O(n)** - Each position is either:
- Computed using mirror property in O(1)
- Expanded, but each expansion increases R

Since R only increases and is bounded by n, total expansions ≤ 2n.

### 4.2 Space Complexity

- Transformed string: O(n)
- P array: O(n)
- **Total**: O(n)

## 5. Visual Representation

### 5.1 Example: S = "babad"

```
Original: b a b a d
Transform: # b # a # b # a # d #
Index:     0 1 2 3 4 5 6 7 8 9 10

P array computation:
Position 0: P[0] = 0 (#)
Position 1: P[1] = 1 (b), palindrome "b"
Position 2: P[2] = 0 (#)
Position 3: P[3] = 3 (a), palindrome "#b#a#b#" → "bab"
Position 4: P[4] = 0 (#)
Position 5: P[5] = 3 (b), palindrome "#a#b#a#" → "aba"
...

P: [0, 1, 0, 3, 0, 3, 0, 1, 0, 1, 0]

Maximum: P[3] = 3 or P[5] = 3
Longest palindrome: "bab" or "aba" (length 3)
```

### 5.2 Mirror Property Visualization

```
        C (center)
        ↓
  # a # b # a # b # a #
      ↑       ↑
   mirror     i
      
If palindrome extends to R:
  [-------palindrome-------]
                           R
  
P[i] starts with min(P[mirror], R-i)
```

### 5.3 State Diagram

```mermaid
flowchart TD
    A[Start i=0] --> B{i < R?}
    B -->|Yes| C[P[i] = min&#40;R-i, P[mirror]&#41;]
    B -->|No| D[P[i] = 0]
    C --> E[Expand while match]
    D --> E
    E --> F{i + P[i] > R?}
    F -->|Yes| G[Update C=i, R=i+P[i]]
    F -->|No| H[Keep C, R]
    G --> I{i < n?}
    H --> I
    I -->|Yes| J[i++]
    J --> B
    I -->|No| K[Find max P[i]]
    K --> L[Extract substring]
```

## 6. Step-by-Step Example

```
String: "cbbd"
Transformed: "#c#b#b#d#"

Step 1: i=0, C=0, R=0
  T: # c # b # b # d #
     ^
  P[0]=0, expand: no match
  R stays 0

Step 2: i=1, C=0, R=0
  T: # c # b # b # d #
       ^
  i≥R, start from 0
  Expand: '#' vs '#', match! P[1]=1
  Expand: out of bounds
  Update: C=1, R=2

Step 3: i=2, C=1, R=2
  T: # c # b # b # d #
         ^
  i≥R, start from 0
  Expand: 'c' vs 'b', no match
  P[2]=0

Step 4: i=3, C=1, R=2
  T: # c # b # b # d #
           ^
  i≥R, start from 0
  Expand: '#' vs '#', match! P[3]=1
  Expand: 'b' vs 'b', match! P[3]=2
  Expand: '#' vs '#', match! P[3]=3
  Expand: 'c' vs 'd', no match
  Update: C=3, R=6

Step 5: i=4, C=3, R=6
  T: # c # b # b # d #
             ^
  mirror = 2*3-4 = 2
  P[4] = min(6-4, P[2]) = min(2, 0) = 0
  Expand: 'b' vs 'b', match! P[4]=1
  Expand: '#' vs '#', match! P[4]=2
  Expand: 'c' vs 'd', no match
  R stays 6 (4+2 = 6)

Final P: [0, 1, 0, 3, 2, 1, 0, 1, 0]
Maximum: P[3]=3, center=3
Start in original: (3-3)/2 = 0, length 3
Result: "bb" (wait, length should be 2)

Actually: maxLen=3 corresponds to "#b#b#" which is "bb" in original
start = (3-3)/2 = 0... hmm

Correction: For "cbbd":
P = [0, 1, 0, 1, 2, 1, 0, 1, 0]
Max at i=4, P[4]=2
Original: start=(4-2)/2=1, length=2
Result: "bb" ✓
```

## 7. Implementation

```python
def manacher(s: str) -> str:
    """
    Find longest palindromic substring using Manacher's algorithm.
    
    >>> manacher("babad")
    'bab'
    >>> manacher("cbbd")
    'bb'
    >>> manacher("a")
    'a'
    >>> manacher("ac")
    'a'
    """
    if not s:
        return ""
    
    # Transform: "abc" -> "#a#b#c#"
    t = '#' + '#'.join(s) + '#'
    n = len(t)
    p = [0] * n
    c = r = 0
    
    for i in range(n):
        # Mirror position
        mirror = 2 * c - i
        
        # Use mirror property if within boundary
        if i < r:
            p[i] = min(r - i, p[mirror])
        
        # Expand palindrome
        while (i + p[i] + 1 < n and 
               i - p[i] - 1 >= 0 and 
               t[i + p[i] + 1] == t[i - p[i] - 1]):
            p[i] += 1
        
        # Update center and right boundary
        if i + p[i] > r:
            c, r = i, i + p[i]
    
    # Find maximum
    max_len = max(p)
    center = p.index(max_len)
    start = (center - max_len) // 2
    
    return s[start:start + max_len]
```

## 8. Related Problems and Extensions

### 8.1 Count All Palindromic Substrings

```python
def count_palindromes(s: str) -> int:
    """Count total palindromic substrings."""
    t = '#' + '#'.join(s) + '#'
    n = len(t)
    p = [0] * n
    c = r = 0
    
    for i in range(n):
        if i < r:
            p[i] = min(r - i, p[2*c - i])
        while (i + p[i] + 1 < n and 
               i - p[i] - 1 >= 0 and 
               t[i + p[i] + 1] == t[i - p[i] - 1]):
            p[i] += 1
        if i + p[i] > r:
            c, r = i, i + p[i]
    
    # Count: each P[i] contains (P[i]+1)//2 palindromes centered at i
    return sum((x + 1) // 2 for x in p)
```

### 8.2 Palindrome at Each Position

```python
def palindrome_lengths(s: str) -> tuple[list, list]:
    """
    Return odd and even palindrome radii at each position.
    odd[i] = radius of longest odd palindrome centered at i
    even[i] = radius of longest even palindrome centered between i and i+1
    """
    n = len(s)
    
    # Odd-length palindromes
    odd = [0] * n
    l, r = 0, -1
    for i in range(n):
        k = 1 if i > r else min(odd[l + r - i], r - i + 1)
        while i - k >= 0 and i + k < n and s[i - k] == s[i + k]:
            k += 1
        odd[i] = k - 1
        if i + odd[i] > r:
            l, r = i - odd[i], i + odd[i]
    
    # Even-length palindromes
    even = [0] * n
    l, r = 0, -1
    for i in range(n):
        k = 0 if i > r else min(even[l + r - i + 1], r - i + 1)
        while i - k - 1 >= 0 and i + k < n and s[i - k - 1] == s[i + k]:
            k += 1
        even[i] = k
        if i + even[i] - 1 > r:
            l, r = i - even[i], i + even[i] - 1
    
    return odd, even
```

## 9. Comparison with Alternatives

| Algorithm | Time | Space | Notes |
|-----------|------|-------|-------|
| **Manacher** | O(n) | O(n) | Optimal, single traversal |
| Expand around center | O(n²) | O(1) | Simpler, slower |
| Dynamic Programming | O(n²) | O(n²) | Easy to implement |
| Suffix Tree | O(n) | O(n) | Complex, versatile |
| Hashing | O(n log n) | O(n) | Binary search + rolling hash |

## 10. Real-World Software Engineering Applications

### 10.1 Industry Use Cases

1. **Bioinformatics**
   - Finding palindromic sequences in DNA (restriction enzyme sites)
   - RNA secondary structure prediction
   - Detecting inverted repeats

2. **Text Processing**
   - Natural language processing for word analysis
   - Spell checking for palindromic typos
   - Text compression

3. **Data Validation**
   - Checking palindromic properties in data
   - Code verification (some checksums are palindromic)

### 10.2 Production Example

```python
class DNAAnalyzer:
    """Analyze palindromic patterns in DNA sequences."""
    
    def __init__(self, sequence: str):
        self.sequence = sequence.upper()
        self._validate()
    
    def _validate(self):
        valid = set('ACGT')
        if not all(c in valid for c in self.sequence):
            raise ValueError("Invalid DNA sequence")
    
    def find_longest_palindrome(self) -> tuple[int, int, str]:
        """Find longest palindromic substring."""
        t = '#' + '#'.join(self.sequence) + '#'
        n = len(t)
        p = [0] * n
        c = r = 0
        
        for i in range(n):
            if i < r:
                p[i] = min(r - i, p[2*c - i])
            while (i + p[i] + 1 < n and 
                   i - p[i] - 1 >= 0 and 
                   t[i + p[i] + 1] == t[i - p[i] - 1]):
                p[i] += 1
            if i + p[i] > r:
                c, r = i, i + p[i]
        
        max_len = max(p)
        center = p.index(max_len)
        start = (center - max_len) // 2
        
        return start, start + max_len, self.sequence[start:start + max_len]
    
    def find_restriction_sites(self, min_length: int = 4) -> list[dict]:
        """
        Find potential restriction enzyme recognition sites.
        These are often palindromic sequences.
        """
        t = '#' + '#'.join(self.sequence) + '#'
        n = len(t)
        p = [0] * n
        c = r = 0
        
        for i in range(n):
            if i < r:
                p[i] = min(r - i, p[2*c - i])
            while (i + p[i] + 1 < n and 
                   i - p[i] - 1 >= 0 and 
                   t[i + p[i] + 1] == t[i - p[i] - 1]):
                p[i] += 1
            if i + p[i] > r:
                c, r = i, i + p[i]
        
        sites = []
        for i, radius in enumerate(p):
            if radius >= min_length:
                start = (i - radius) // 2
                length = radius
                sites.append({
                    'position': start,
                    'length': length,
                    'sequence': self.sequence[start:start + length]
                })
        
        return sites
```

## 11. Edge Cases

| Edge Case | Result | Notes |
|-----------|--------|-------|
| Empty string | "" | Return empty |
| Single character | "a" | Trivial palindrome |
| No palindrome > 1 | First char | "abc" → "a" |
| All same chars | Entire string | "aaaa" → "aaaa" |
| Even palindrome | Handle correctly | "abba" → "abba" |

## 12. References

- Manacher, G. (1975). "A New Linear-Time 'On-Line' Algorithm for Finding the Smallest Initial Palindrome of a String"
- [Wikipedia: Longest palindromic substring](https://en.wikipedia.org/wiki/Longest_palindromic_substring)
- [CP-Algorithms: Manacher's Algorithm](https://cp-algorithms.com/string/manacher.html)
