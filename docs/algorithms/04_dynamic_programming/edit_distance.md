# Edit Distance (Levenshtein Distance)

| Property | Value |
|----------|-------|
| Category | Dynamic Programming |
| Subcategory | String Transformation |
| Complexity (Time) | O(mn) |
| Complexity (Space) | O(mn) or O(min(m,n)) optimized |
| Input | Two strings |
| Output | Minimum edit operations |

## Overview

**Edit Distance** (also known as Levenshtein Distance) is the minimum number of single-character operations required to transform one string into another. The allowed operations are:
- **Insert** a character
- **Delete** a character
- **Replace** a character

This algorithm is fundamental in spell checkers, DNA sequence alignment, natural language processing, and fuzzy string matching.

## Mathematical Foundation

### Problem Definition

Given strings $X$ of length $m$ and $Y$ of length $n$, find the minimum number of edit operations to transform $X$ into $Y$.

### Recurrence Relation

Let $D[i][j]$ = edit distance between $X[1..i]$ and $Y[1..j]$:

$$D[i][j] = \begin{cases} 
j & \text{if } i = 0 \\
i & \text{if } j = 0 \\
D[i-1][j-1] & \text{if } x_i = y_j \\
1 + \min \begin{cases}
D[i-1][j] & \text{(delete)} \\
D[i][j-1] & \text{(insert)} \\
D[i-1][j-1] & \text{(replace)}
\end{cases} & \text{otherwise}
\end{cases}$$

### Operation Semantics

For transforming $X$ to $Y$:
- **Delete**: Remove character from $X$ → Move from $D[i][j]$ to $D[i-1][j]$
- **Insert**: Add character to $X$ → Move from $D[i][j]$ to $D[i][j-1]$
- **Replace**: Change character in $X$ → Move from $D[i][j]$ to $D[i-1][j-1]$

### Distance Properties

Edit distance is a **metric**:
1. **Non-negativity**: $D(X, Y) \geq 0$
2. **Identity**: $D(X, Y) = 0 \iff X = Y$
3. **Symmetry**: $D(X, Y) = D(Y, X)$
4. **Triangle inequality**: $D(X, Z) \leq D(X, Y) + D(Y, Z)$

## Algorithm Approaches

### 1. Recursive (Exponential)

```
EDIT-DISTANCE-RECURSIVE(X, Y, m, n):
    if m == 0:
        return n
    if n == 0:
        return m
    
    if X[m-1] == Y[n-1]:
        return EDIT-DISTANCE-RECURSIVE(X, Y, m-1, n-1)
    
    return 1 + min(
        EDIT-DISTANCE-RECURSIVE(X, Y, m-1, n),     // Delete
        EDIT-DISTANCE-RECURSIVE(X, Y, m, n-1),     // Insert
        EDIT-DISTANCE-RECURSIVE(X, Y, m-1, n-1)    // Replace
    )
```

### 2. Memoization (Top-Down)

```
EDIT-DISTANCE-MEMO(X, Y):
    m = length(X)
    n = length(Y)
    memo = empty map
    
    function SOLVE(i, j):
        if i == 0:
            return j
        if j == 0:
            return i
        
        if (i, j) in memo:
            return memo[(i, j)]
        
        if X[i-1] == Y[j-1]:
            result = SOLVE(i-1, j-1)
        else:
            result = 1 + min(
                SOLVE(i-1, j),      // Delete
                SOLVE(i, j-1),      // Insert
                SOLVE(i-1, j-1)     // Replace
            )
        
        memo[(i, j)] = result
        return result
    
    return SOLVE(m, n)
```

### 3. Tabulation (Bottom-Up)

```
EDIT-DISTANCE-DP(X, Y):
    m = length(X)
    n = length(Y)
    
    // Create DP table
    D = 2D array of size (m+1) × (n+1)
    
    // Initialize base cases
    for i from 0 to m:
        D[i][0] = i    // Delete all from X
    for j from 0 to n:
        D[0][j] = j    // Insert all to empty string
    
    // Fill table
    for i from 1 to m:
        for j from 1 to n:
            if X[i-1] == Y[j-1]:
                D[i][j] = D[i-1][j-1]
            else:
                D[i][j] = 1 + min(
                    D[i-1][j],      // Delete
                    D[i][j-1],      // Insert
                    D[i-1][j-1]     // Replace
                )
    
    return D[m][n]
```

### 4. Space-Optimized

```
EDIT-DISTANCE-OPTIMIZED(X, Y):
    m = length(X)
    n = length(Y)
    
    // Use two rows
    prev = [0, 1, 2, ..., n]
    curr = array of size (n+1)
    
    for i from 1 to m:
        curr[0] = i
        for j from 1 to n:
            if X[i-1] == Y[j-1]:
                curr[j] = prev[j-1]
            else:
                curr[j] = 1 + min(prev[j], curr[j-1], prev[j-1])
        prev = copy of curr
    
    return prev[n]
```

### 5. Operation Reconstruction

```
EDIT-OPERATIONS(X, Y):
    m = length(X)
    n = length(Y)
    
    // Build DP table
    D = 2D array of size (m+1) × (n+1)
    // ... (fill as in tabulation)
    
    // Backtrack to find operations
    operations = empty list
    i = m, j = n
    
    while i > 0 or j > 0:
        if i > 0 and j > 0 and X[i-1] == Y[j-1]:
            // No operation needed
            i = i - 1
            j = j - 1
        else if i > 0 and j > 0 and D[i][j] == D[i-1][j-1] + 1:
            operations.append(("Replace", i-1, Y[j-1]))
            i = i - 1
            j = j - 1
        else if j > 0 and D[i][j] == D[i][j-1] + 1:
            operations.append(("Insert", i, Y[j-1]))
            j = j - 1
        else:
            operations.append(("Delete", i-1, X[i-1]))
            i = i - 1
    
    return reverse(operations)
```

## Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Naive Recursion | O(3^max(m,n)) | O(m+n) | Exponential |
| Memoization | O(mn) | O(mn) | Top-down |
| Tabulation | O(mn) | O(mn) | Bottom-up |
| Space-Optimized | O(mn) | O(min(m,n)) | Distance only |
| With Operations | O(mn) | O(mn) | Full reconstruction |

## Visual Representation

### DP Table Construction

```
X = "HORSE"
Y = "ROS"

        ""   R   O   S
    ┌───────────────────┐
 "" │  0   1   2   3    │ ← Insert all of Y
    │                   │
 H  │  1   1   2   3    │ H≠R: min(0+1, 1+1, 1+1) = 1
    │     ↖            │
 O  │  2   2   1   2    │ O=O: take diagonal = 1
    │         ↖        │
 R  │  3   2   2   2    │ R=R from (1,1): 2, then min
    │     ↖            │
 S  │  4   3   3   2    │ S=S: take diagonal = 2
    │             ↖    │
 E  │  5   4   4   3    │ E≠S: min(2+1, 4+1, 2+1) = 3
    │                   │
    └───────────────────┘

Edit Distance = 3
```

### Operation Sequence

```mermaid
graph LR
    A["HORSE"] -->|"Replace H→R"| B["RORSE"]
    B -->|"Delete R"| C["ROSE"]
    C -->|"Delete E"| D["ROS"]
    
    style A fill:#ff9999
    style D fill:#90EE90
```

### Decision at Each Cell

```
For D[i][j] where X[i-1] ≠ Y[j-1]:

            D[i-1][j-1]     D[i-1][j]
        (Replace cost)   (Delete cost)
                  ↘           ↓
                    D[i][j]
                  ↗
              D[i][j-1]
          (Insert cost)

D[i][j] = 1 + min(Replace, Delete, Insert)
```

## Implementation (from repository)

```python
class EditDistance:
    """
    Calculate edit distance between two strings.
    
    Supports both top-down (memoization) and bottom-up (tabulation) approaches.
    """
    
    def __init__(self, source: str, target: str) -> None:
        """
        Initialize with source and target strings.
        
        Args:
            source: String to transform from
            target: String to transform to
        """
        self.source = source
        self.target = target
        self.m = len(source)
        self.n = len(target)
    
    def min_dist_top_down(self) -> int:
        """
        Calculate edit distance using memoization (top-down).
        
        >>> EditDistance("horse", "ros").min_dist_top_down()
        3
        >>> EditDistance("", "abc").min_dist_top_down()
        3
        >>> EditDistance("abc", "abc").min_dist_top_down()
        0
        """
        memo = {}
        
        def dp(i: int, j: int) -> int:
            if i == 0:
                return j
            if j == 0:
                return i
            
            if (i, j) in memo:
                return memo[(i, j)]
            
            if self.source[i - 1] == self.target[j - 1]:
                result = dp(i - 1, j - 1)
            else:
                result = 1 + min(
                    dp(i - 1, j),      # Delete
                    dp(i, j - 1),      # Insert
                    dp(i - 1, j - 1)   # Replace
                )
            
            memo[(i, j)] = result
            return result
        
        return dp(self.m, self.n)
    
    def min_dist_bottom_up(self) -> int:
        """
        Calculate edit distance using tabulation (bottom-up).
        
        >>> EditDistance("intention", "execution").min_dist_bottom_up()
        5
        >>> EditDistance("kitten", "sitting").min_dist_bottom_up()
        3
        """
        # Create DP table
        dp = [[0] * (self.n + 1) for _ in range(self.m + 1)]
        
        # Initialize base cases
        for i in range(self.m + 1):
            dp[i][0] = i
        for j in range(self.n + 1):
            dp[0][j] = j
        
        # Fill table
        for i in range(1, self.m + 1):
            for j in range(1, self.n + 1):
                if self.source[i - 1] == self.target[j - 1]:
                    dp[i][j] = dp[i - 1][j - 1]
                else:
                    dp[i][j] = 1 + min(
                        dp[i - 1][j],      # Delete
                        dp[i][j - 1],      # Insert
                        dp[i - 1][j - 1]   # Replace
                    )
        
        return dp[self.m][self.n]
```

## Real-World Applications

### 1. Spell Checker with Suggestions

```python
from typing import List, Tuple

def spell_check(word: str, dictionary: List[str], 
                max_distance: int = 2,
                max_suggestions: int = 5) -> List[Tuple[str, int]]:
    """
    Find spelling suggestions within edit distance threshold.
    
    >>> dictionary = ["hello", "help", "held", "hall", "world"]
    >>> suggestions = spell_check("helo", dictionary)
    >>> suggestions[0][0]  # Best match
    'hello'
    """
    def edit_distance(s1: str, s2: str) -> int:
        m, n = len(s1), len(s2)
        
        # Early termination optimization
        if abs(m - n) > max_distance:
            return max_distance + 1
        
        # Space-optimized DP
        prev = list(range(n + 1))
        curr = [0] * (n + 1)
        
        for i in range(1, m + 1):
            curr[0] = i
            for j in range(1, n + 1):
                if s1[i-1] == s2[j-1]:
                    curr[j] = prev[j-1]
                else:
                    curr[j] = 1 + min(prev[j], curr[j-1], prev[j-1])
            prev, curr = curr, [0] * (n + 1)
        
        return prev[n]
    
    # Find all words within threshold
    candidates = []
    for dict_word in dictionary:
        dist = edit_distance(word.lower(), dict_word.lower())
        if dist <= max_distance:
            candidates.append((dict_word, dist))
    
    # Sort by distance, then alphabetically
    candidates.sort(key=lambda x: (x[1], x[0]))
    
    return candidates[:max_suggestions]


def auto_correct(text: str, dictionary: List[str]) -> str:
    """
    Auto-correct a sentence using edit distance.
    
    >>> dictionary = ["the", "quick", "brown", "fox"]
    >>> auto_correct("teh quik brwn fox", dictionary)
    'the quick brown fox'
    """
    words = text.split()
    corrected = []
    
    for word in words:
        suggestions = spell_check(word, dictionary, max_distance=2)
        if suggestions and suggestions[0][1] > 0:
            corrected.append(suggestions[0][0])
        else:
            corrected.append(word)
    
    return ' '.join(corrected)
```

### 2. DNA Sequence Alignment

```python
from typing import Tuple, List

def dna_alignment(seq1: str, seq2: str, 
                  match: int = 0, 
                  mismatch: int = 1,
                  gap: int = 1) -> Tuple[int, str, str]:
    """
    Align DNA sequences with custom scoring.
    
    Returns alignment score and aligned sequences with gaps.
    
    >>> score, aligned1, aligned2 = dna_alignment("ATCG", "ACG")
    >>> score
    1
    >>> '-' in aligned1 or '-' in aligned2
    True
    """
    m, n = len(seq1), len(seq2)
    
    # DP table
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    # Initialize
    for i in range(m + 1):
        dp[i][0] = i * gap
    for j in range(n + 1):
        dp[0][j] = j * gap
    
    # Fill table
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if seq1[i-1] == seq2[j-1]:
                dp[i][j] = dp[i-1][j-1] + match
            else:
                dp[i][j] = min(
                    dp[i-1][j-1] + mismatch,  # Mismatch
                    dp[i-1][j] + gap,          # Gap in seq2
                    dp[i][j-1] + gap           # Gap in seq1
                )
    
    # Backtrack for alignment
    aligned1, aligned2 = [], []
    i, j = m, n
    
    while i > 0 or j > 0:
        if i > 0 and j > 0 and seq1[i-1] == seq2[j-1]:
            aligned1.append(seq1[i-1])
            aligned2.append(seq2[j-1])
            i -= 1
            j -= 1
        elif i > 0 and j > 0 and dp[i][j] == dp[i-1][j-1] + mismatch:
            aligned1.append(seq1[i-1])
            aligned2.append(seq2[j-1])
            i -= 1
            j -= 1
        elif i > 0 and dp[i][j] == dp[i-1][j] + gap:
            aligned1.append(seq1[i-1])
            aligned2.append('-')
            i -= 1
        else:
            aligned1.append('-')
            aligned2.append(seq2[j-1])
            j -= 1
    
    return dp[m][n], ''.join(reversed(aligned1)), ''.join(reversed(aligned2))
```

### 3. Fuzzy Search for Database Queries

```python
from typing import List, Tuple, Dict

class FuzzySearchIndex:
    """
    Fuzzy search index using edit distance.
    
    Useful for searching user names, product titles, etc.
    """
    
    def __init__(self, items: List[str]) -> None:
        """
        Build index from list of searchable items.
        
        >>> index = FuzzySearchIndex(["apple", "application", "banana"])
        >>> len(index.items)
        3
        """
        self.items = items
        self._build_ngram_index()
    
    def _build_ngram_index(self, n: int = 3) -> None:
        """Build n-gram index for faster candidate filtering."""
        self.ngram_index: Dict[str, set] = {}
        
        for idx, item in enumerate(self.items):
            padded = f"__{item.lower()}__"
            for i in range(len(padded) - n + 1):
                ngram = padded[i:i+n]
                if ngram not in self.ngram_index:
                    self.ngram_index[ngram] = set()
                self.ngram_index[ngram].add(idx)
    
    def _get_candidates(self, query: str, n: int = 3) -> set:
        """Get candidate indices using n-gram overlap."""
        padded = f"__{query.lower()}__"
        candidates = set()
        
        for i in range(len(padded) - n + 1):
            ngram = padded[i:i+n]
            if ngram in self.ngram_index:
                candidates.update(self.ngram_index[ngram])
        
        return candidates
    
    def search(self, query: str, max_distance: int = 2,
               max_results: int = 10) -> List[Tuple[str, int]]:
        """
        Search for items similar to query.
        
        >>> index = FuzzySearchIndex(["python", "pyhton", "java", "javascript"])
        >>> results = index.search("pytohn", max_distance=2)
        >>> results[0][0] in ["python", "pyhton"]
        True
        """
        query_lower = query.lower()
        candidates = self._get_candidates(query)
        
        results = []
        for idx in candidates:
            item = self.items[idx]
            dist = self._edit_distance(query_lower, item.lower())
            if dist <= max_distance:
                results.append((item, dist))
        
        # Sort by distance, then by length difference
        results.sort(key=lambda x: (x[1], abs(len(x[0]) - len(query))))
        
        return results[:max_results]
    
    @staticmethod
    def _edit_distance(s1: str, s2: str) -> int:
        """Calculate edit distance with early termination."""
        m, n = len(s1), len(s2)
        
        prev = list(range(n + 1))
        curr = [0] * (n + 1)
        
        for i in range(1, m + 1):
            curr[0] = i
            for j in range(1, n + 1):
                if s1[i-1] == s2[j-1]:
                    curr[j] = prev[j-1]
                else:
                    curr[j] = 1 + min(prev[j], curr[j-1], prev[j-1])
            prev, curr = curr, [0] * (n + 1)
        
        return prev[n]
```

### 4. Command Line Suggestion

```python
from typing import List, Tuple

def suggest_command(user_input: str, 
                   commands: List[str]) -> List[Tuple[str, int, str]]:
    """
    Suggest similar commands for typos in CLI.
    
    Returns suggestions with distance and description.
    
    >>> commands = ["git commit", "git push", "git pull", "git status"]
    >>> suggestions = suggest_command("git comit", commands)
    >>> suggestions[0][0]
    'git commit'
    """
    def edit_distance(s1: str, s2: str) -> int:
        m, n = len(s1), len(s2)
        prev = list(range(n + 1))
        curr = [0] * (n + 1)
        
        for i in range(1, m + 1):
            curr[0] = i
            for j in range(1, n + 1):
                cost = 0 if s1[i-1] == s2[j-1] else 1
                curr[j] = min(
                    prev[j] + 1,
                    curr[j-1] + 1,
                    prev[j-1] + cost
                )
            prev, curr = curr, [0] * (n + 1)
        return prev[n]
    
    suggestions = []
    for cmd in commands:
        dist = edit_distance(user_input.lower(), cmd.lower())
        # Only suggest if reasonably close
        threshold = max(2, len(user_input) // 3)
        if dist <= threshold:
            suggestions.append((cmd, dist, f"Did you mean '{cmd}'?"))
    
    suggestions.sort(key=lambda x: x[1])
    return suggestions[:3]
```

## Variations

### Damerau-Levenshtein Distance (Includes Transposition)

```python
def damerau_levenshtein(s1: str, s2: str) -> int:
    """
    Edit distance including transposition of adjacent characters.
    
    >>> damerau_levenshtein("ab", "ba")  # One transposition
    1
    >>> damerau_levenshtein("ca", "abc")
    2
    """
    m, n = len(s1), len(s2)
    
    # Create table with extra row/col for transposition check
    d = [[0] * (n + 2) for _ in range(m + 2)]
    
    max_dist = m + n
    d[0][0] = max_dist
    
    for i in range(m + 1):
        d[i + 1][0] = max_dist
        d[i + 1][1] = i
    
    for j in range(n + 1):
        d[0][j + 1] = max_dist
        d[1][j + 1] = j
    
    # Last position of each character
    last_pos = {}
    
    for i in range(1, m + 1):
        last_match = 0
        for j in range(1, n + 1):
            k = last_pos.get(s2[j - 1], 0)
            l = last_match
            
            if s1[i - 1] == s2[j - 1]:
                d[i + 1][j + 1] = d[i][j]
                last_match = j
            else:
                d[i + 1][j + 1] = min(
                    d[i][j],           # Replace
                    d[i + 1][j],       # Insert
                    d[i][j + 1]        # Delete
                ) + 1
            
            # Transposition
            if k > 0 and l > 0:
                d[i + 1][j + 1] = min(
                    d[i + 1][j + 1],
                    d[k][l] + (i - k - 1) + 1 + (j - l - 1)
                )
        
        last_pos[s1[i - 1]] = i
    
    return d[m + 1][n + 1]
```

### Weighted Edit Distance

```python
def weighted_edit_distance(s1: str, s2: str,
                          insert_cost: float = 1.0,
                          delete_cost: float = 1.0,
                          replace_cost: float = 1.5) -> float:
    """
    Edit distance with custom operation costs.
    
    >>> weighted_edit_distance("abc", "adc", replace_cost=2.0)
    2.0
    """
    m, n = len(s1), len(s2)
    
    dp = [[0.0] * (n + 1) for _ in range(m + 1)]
    
    for i in range(m + 1):
        dp[i][0] = i * delete_cost
    for j in range(n + 1):
        dp[0][j] = j * insert_cost
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if s1[i-1] == s2[j-1]:
                dp[i][j] = dp[i-1][j-1]
            else:
                dp[i][j] = min(
                    dp[i-1][j] + delete_cost,
                    dp[i][j-1] + insert_cost,
                    dp[i-1][j-1] + replace_cost
                )
    
    return dp[m][n]
```

## Common Pitfalls

1. **Off-by-one errors**: Table is (m+1)×(n+1), strings are 0-indexed
2. **Forgetting base cases**: First row/column must be initialized
3. **Operation direction**: Delete from source, Insert to match target
4. **Symmetry assumption**: Same distance both ways, but operations differ

## References

- [Levenshtein Distance - Wikipedia](https://en.wikipedia.org/wiki/Levenshtein_distance)
- [Edit Distance - GeeksforGeeks](https://www.geeksforgeeks.org/edit-distance-dp-5/)
- Wagner-Fischer Algorithm (1974)
- CLRS Chapter 15.5

## See Also

- [Longest Common Subsequence](longest_common_subsequence.md) - Related string DP
- [Smith-Waterman](smith_waterman.md) - Local sequence alignment
- [Regex Matching](regex_match.md) - Pattern matching DP
