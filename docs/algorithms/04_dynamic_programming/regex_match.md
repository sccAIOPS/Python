# Regular Expression Matching

## Overview
| Property | Value |
|----------|-------|
| **Category** | Dynamic Programming, String Matching |
| **Complexity (Time)** | O(m × n) |
| **Complexity (Space)** | O(m × n) |
| **Input** | String s, Pattern p |
| **Output** | Boolean (match result) |
| **Source** | [regex_match.py](../../../dynamic_programming/regex_match.py) |

## 1. Mathematical Foundation

### 1.1 Problem Definition

Given an input string `s` and a pattern `p`, implement regular expression matching with support for `.` and `*`:

- `.` matches any single character
- `*` matches zero or more of the preceding element

$$
\text{match}(s, p) = 
\begin{cases}
\text{true} & \text{if } s \text{ matches } p \\
\text{false} & \text{otherwise}
\end{cases}
$$

### 1.2 Recurrence Relation

Let $dp[i][j]$ denote whether $s[0..i-1]$ matches $p[0..j-1]$.

**Base Cases:**
$$dp[0][0] = \text{true}$$
$$dp[i][0] = \text{false} \quad \forall i > 0$$
$$dp[0][j] = dp[0][j-2] \quad \text{if } p[j-1] = '*'$$

**Recurrence:**

If $p[j-1] \neq '*'$:
$$dp[i][j] = dp[i-1][j-1] \land (s[i-1] = p[j-1] \lor p[j-1] = '.')$$

If $p[j-1] = '*'$:
$$dp[i][j] = dp[i][j-2] \lor (dp[i-1][j] \land (s[i-1] = p[j-2] \lor p[j-2] = '.'))$$

### 1.3 Proof of Correctness

The solution builds on the principle of optimal substructure:
- A match for $s[0..i]$ against $p[0..j]$ depends on matches of smaller substrings
- The `*` operator creates branching: match zero occurrences (skip pattern) or match one+ occurrences

## 2. Algorithm Description

### 2.1 Intuition

Build a 2D table where each cell represents whether a prefix of the string matches a prefix of the pattern. Handle the `*` operator by considering two possibilities:
1. Match zero occurrences of the preceding character
2. Match one or more occurrences

### 2.2 Step-by-Step Process

1. Create DP table of size $(|s|+1) \times (|p|+1)$
2. Initialize base cases
3. Fill table row by row
4. Return $dp[|s|][|p|]$

## 3. Pseudocode

```
ALGORITHM RegexMatch(s, p)
    INPUT: String s of length m, Pattern p of length n
    OUTPUT: Boolean indicating if s matches p
    
    1. dp ← 2D array of size (m+1) × (n+1), initialized to false
    2. dp[0][0] ← true
    
    3. // Handle patterns like a*, a*b*, a*b*c*
       for j ← 2 to n do
           if p[j-1] = '*' then
               dp[0][j] ← dp[0][j-2]
           end if
       end for
    
    4. for i ← 1 to m do
           for j ← 1 to n do
               if p[j-1] = '*' then
                   // Zero occurrences OR one+ occurrences
                   dp[i][j] ← dp[i][j-2] OR 
                              (dp[i-1][j] AND (s[i-1] = p[j-2] OR p[j-2] = '.'))
               else if p[j-1] = '.' OR s[i-1] = p[j-1] then
                   dp[i][j] ← dp[i-1][j-1]
               end if
           end for
       end for
    
    5. return dp[m][n]
```

## 4. Complexity Analysis

### 4.1 Time Complexity

| Case | Complexity | Explanation |
|------|------------|-------------|
| All | O(m × n) | Fill entire DP table once |

Where m = length of string, n = length of pattern.

### 4.2 Space Complexity

| Approach | Complexity | Notes |
|----------|------------|-------|
| Standard DP | O(m × n) | Full 2D table |
| Space-optimized | O(n) | Only two rows needed |

## 5. Visual Representation

### Example: s = "aab", p = "c*a*b"

```
      ""   c    *    a    *    b
  ""   T   F    T    F    T    F
  a    F   F    F    T    T    F
  a    F   F    F    F    T    F
  b    F   F    F    F    F    T
```

```mermaid
flowchart TD
    A[Start: s='aab', p='c*a*b'] --> B{p[1]='*'?}
    B -->|Yes| C[c* matches 0 chars]
    C --> D{p[3]='*'?}
    D -->|Yes| E[a* matches 'aa']
    E --> F{p[4]='b' = s[2]?}
    F -->|Yes| G[Match: TRUE]
```

## 6. Implementation Notes

### 6.1 Key Data Structures
- **2D Boolean Array**: Stores match results for subproblems
- **Two Pointers**: Track positions in string and pattern

### 6.2 Edge Cases

| Edge Case | Expected Result |
|-----------|-----------------|
| Empty string, empty pattern | True |
| Empty string, pattern "a*" | True |
| Empty string, pattern "a" | False |
| String "a", pattern ".*" | True |
| String "aa", pattern "a" | False |

## 7. Comparison with Related Algorithms

| Algorithm | Time | Space | Features |
|-----------|------|-------|----------|
| Regex Match (DP) | O(mn) | O(mn) | Supports `.` and `*` |
| Wildcard Matching | O(mn) | O(mn) | Supports `?` and `*` |
| KMP | O(m+n) | O(n) | Exact pattern only |
| NFA-based Regex | O(mn) | O(n) | Full regex support |

## 8. Real-World Software Engineering Applications

### 8.1 Industry Use Cases

1. **Text Editors & IDEs**
   - Find and replace functionality
   - Syntax highlighting patterns
   - Code search features

2. **Log Analysis Systems**
   - Pattern matching in log files
   - Error detection and categorization
   - Security event correlation

3. **Input Validation**
   - Email format validation
   - Phone number parsing
   - URL validation

4. **Database Systems**
   - SQL LIKE operator implementation
   - Full-text search engines
   - Query optimization

5. **Network Security**
   - Intrusion detection systems
   - Packet filtering rules
   - Content filtering

### 8.2 Libraries Using This Algorithm

| Library/Tool | Usage |
|--------------|-------|
| Python `re` module | Regex engine implementation |
| grep/awk/sed | Unix text processing |
| Elasticsearch | Query DSL patterns |
| nginx | Location block matching |

### 8.3 Production Example

```python
# Input validation in web applications
def validate_email(email: str) -> bool:
    """
    Simplified email validation using regex matching concept.
    Pattern: .*@.*\\..*
    """
    # In practice, use compiled regex patterns
    import re
    pattern = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
    return bool(re.match(pattern, email))
```

## 9. References

- [LeetCode Problem 10: Regular Expression Matching](https://leetcode.com/problems/regular-expression-matching/)
- Thompson, K. (1968). "Programming Techniques: Regular expression search algorithm"
- [Wikipedia: Regular Expression](https://en.wikipedia.org/wiki/Regular_expression)
