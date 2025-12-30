# Boyer-Moore String Search Algorithm

## Overview
- **Category**: String Pattern Matching
- **Complexity**: Time: O(nm) worst case, O(n/m) best case | Space: O(k) where k is alphabet size
- **Type**: Exact pattern matching
- **Source File**: [strings/boyer_moore_search.py](../../../strings/boyer_moore_search.py)

## 1. Mathematical Foundation

### 1.1 Problem Definition

Given a text $T[1..n]$ and a pattern $P[1..m]$, find all occurrences of $P$ in $T$.

$$
\text{Find all } i \text{ such that } T[i..i+m-1] = P[1..m]
$$

### 1.2 Core Concepts

Boyer-Moore uses two heuristics to skip comparisons:

**Bad Character Rule:**
$$
\text{shift} = \max(1, j - \text{rightmost}[T[s+j]])
$$

Where $j$ is the mismatch position and $\text{rightmost}[c]$ is the rightmost occurrence of character $c$ in pattern.

**Good Suffix Rule:**
$$
\text{shift} = \min(\text{suffixShift}[j], m - j + 1)
$$

### 1.3 Preprocessing

**Bad Character Table:**
$$
R[c] = \max\{j : P[j] = c\} \text{ or } -1 \text{ if } c \notin P
$$

**Good Suffix Table:**
$$
L[i] = \text{largest } j < m \text{ such that } P[i..m] \text{ matches suffix of } P[1..j]
$$

## 2. Algorithm Description

### 2.1 Intuition

Unlike naive algorithms that scan left-to-right, Boyer-Moore:
1. Compares pattern from **right to left**
2. Uses mismatch information to make **large jumps**
3. Can skip portions of text without examining every character

### 2.2 Key Insight

When a mismatch occurs at position $j$ in the pattern:
- The bad character rule tells us where that character appears elsewhere in the pattern
- The good suffix rule uses successfully matched suffix information

## 3. Pseudocode

```
ALGORITHM Boyer-Moore(T, P)
    INPUT: Text T of length n, Pattern P of length m
    OUTPUT: All starting positions where P occurs in T
    
    1. PREPROCESS(P):
       // Build bad character table R
       R ← array of size Σ, initialized to -1
       for j ← 0 to m-1 do
           R[P[j]] ← j
       
       // Build good suffix table L
       L ← ComputeGoodSuffixTable(P)
    
    2. SEARCH:
       s ← 0  // shift of pattern with respect to text
       matches ← empty list
       
       while s ≤ n - m do
           j ← m - 1  // start from rightmost character
           
           while j ≥ 0 AND P[j] = T[s + j] do
               j ← j - 1
           
           if j < 0 then
               // Pattern found
               matches.append(s)
               s ← s + L[0]  // shift using good suffix
           else
               // Mismatch at position j
               badCharShift ← j - R[T[s + j]]
               goodSuffixShift ← L[j + 1]
               s ← s + max(badCharShift, goodSuffixShift)
       
       return matches

ALGORITHM ComputeGoodSuffixTable(P)
    m ← length(P)
    L ← array of size m + 1
    
    // Case 1: Matching suffix appears elsewhere in pattern
    suffix ← ComputeSuffixArray(P)
    for i ← 0 to m do
        L[i] ← m
    
    for j ← m - 1 downto 0 do
        if suffix[j] = j + 1 then
            for i ← 0 to m - j - 1 do
                if L[i] = m then
                    L[i] ← m - j - 1
    
    // Case 2: Part of matching suffix at beginning
    for j ← 0 to m - 2 do
        L[m - suffix[j]] ← m - j - 1
    
    return L
```

## 4. Complexity Analysis

### 4.1 Time Complexity

| Case | Complexity | Scenario |
|------|------------|----------|
| Best | O(n/m) | Large alphabet, random text |
| Average | O(n/m) | Typical text search |
| Worst | O(nm) | Pathological patterns (e.g., "aaa...a" in "aaa...a") |

### 4.2 Space Complexity
- Bad character table: $O(|\Sigma|)$ where $|\Sigma|$ is alphabet size
- Good suffix table: $O(m)$
- Total: $O(m + |\Sigma|)$

### 4.3 Why It's Fast

The algorithm achieves **sublinear** time because:
- Longer patterns lead to larger potential jumps
- Large alphabets reduce collision probability
- Average case skips $m - 1$ characters per comparison

## 5. Visual Representation

```
Text:    A B A A B A C A B A C A B
Pattern: A B A C A B

Step 1: Compare from right
         A B A A B A C A B A C A B
         A B A C A B
                 ↑ mismatch at 'A' vs 'C'
         
         Bad char 'A' is at position 4 in pattern
         Shift = 5 - 4 = 1? No, use max shift = 2

Step 2: After shift
         A B A A B A C A B A C A B
             A B A C A B
                     ↑ Compare from right...

Step 3: Continue until match found
         A B A A B A C A B A C A B
                     A B A C A B
                     ✓ Match found at position 6
```

```mermaid
flowchart TD
    A[Start at s=0] --> B[Compare P from right]
    B --> C{All chars match?}
    C -->|Yes| D[Report match at s]
    C -->|No| E[Calculate shifts]
    D --> F[Apply good suffix shift]
    E --> G[Bad char shift = j - R[T[s+j]]]
    E --> H[Good suffix shift = L[j+1]]
    F --> I{s ≤ n-m?}
    G --> J[s = s + max shifts]
    H --> J
    J --> I
    I -->|Yes| B
    I -->|No| K[Return all matches]
```

## 6. Comparison with Other Algorithms

| Algorithm | Preprocessing | Search | Best For |
|-----------|--------------|--------|----------|
| **Boyer-Moore** | O(m + Σ) | O(n/m) avg | Long patterns, large alphabets |
| Knuth-Morris-Pratt | O(m) | O(n) | Small alphabets, streaming |
| Rabin-Karp | O(m) | O(n) avg | Multiple pattern search |
| Naive | O(1) | O(nm) | Very short patterns |

## 7. Implementation Variants

### 7.1 Boyer-Moore-Horspool
Simplified version using only bad character rule:

```python
def boyer_moore_horspool(text: str, pattern: str) -> list[int]:
    """Simplified Boyer-Moore using only bad character rule."""
    m, n = len(pattern), len(text)
    if m > n:
        return []
    
    # Build bad character table
    skip = {c: m for c in set(text)}
    for i in range(m - 1):
        skip[pattern[i]] = m - i - 1
    
    matches = []
    s = 0
    while s <= n - m:
        j = m - 1
        while j >= 0 and pattern[j] == text[s + j]:
            j -= 1
        if j < 0:
            matches.append(s)
            s += 1
        else:
            s += skip.get(text[s + m - 1], m)
    
    return matches
```

### 7.2 Turbo Boyer-Moore
Remembers previously matched characters to avoid re-comparison.

## 8. Real-World Software Engineering Applications

### 8.1 Industry Use Cases

1. **Text Editors (grep, sed, vim)**
   - Fast pattern searching in large files
   - Real-time search-as-you-type
   - Regular expression engines

2. **Intrusion Detection Systems**
   - Network packet deep inspection
   - Signature-based malware detection
   - Log file analysis (Snort, Suricata)

3. **Bioinformatics**
   - DNA sequence alignment
   - Protein motif searching
   - Genome assembly

4. **Plagiarism Detection**
   - Document similarity checking
   - Code clone detection

### 8.2 Production Example

```python
class LogAnalyzer:
    """High-performance log analyzer using Boyer-Moore."""
    
    def __init__(self, error_patterns: list[str]):
        self.patterns = error_patterns
        self.preprocessed = [
            self._preprocess(p) for p in error_patterns
        ]
    
    def _preprocess(self, pattern: str) -> dict:
        """Build bad character table."""
        table = {}
        for i, c in enumerate(pattern[:-1]):
            table[c] = len(pattern) - i - 1
        return {'pattern': pattern, 'skip': table}
    
    def scan_logs(self, log_file: str) -> dict[str, list[int]]:
        """Scan log file for error patterns."""
        results = {p: [] for p in self.patterns}
        
        with open(log_file, 'r') as f:
            for line_num, line in enumerate(f, 1):
                for prep in self.preprocessed:
                    if self._search(line, prep):
                        results[prep['pattern']].append(line_num)
        
        return results
```

### 8.3 Libraries Using Boyer-Moore

| Library | Language | Usage |
|---------|----------|-------|
| **GNU grep** | C | Default search algorithm |
| **Python re** | Python | Literal string optimization |
| **std::search** | C++ | Pattern matching |
| **StringSearch** | Java | Apache Commons implementation |

## 9. Edge Cases and Gotchas

| Edge Case | Handling |
|-----------|----------|
| Empty pattern | Return empty or all positions |
| Pattern longer than text | Return empty immediately |
| Single character pattern | Degrades to linear scan |
| Repeated characters | Good suffix rule essential |
| Case sensitivity | Preprocess both to same case |

## 10. References

- Boyer, R.S., Moore, J.S. (1977). "A Fast String Searching Algorithm"
- [Wikipedia: Boyer-Moore](https://en.wikipedia.org/wiki/Boyer%E2%80%93Moore_string-search_algorithm)
- Horspool, R.N. (1980). "Practical Fast Searching in Strings"
