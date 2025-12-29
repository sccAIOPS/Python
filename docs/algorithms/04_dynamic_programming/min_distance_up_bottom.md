# Edit Distance (Top-Down / Memoization)

| Property | Value |
|----------|-------|
| Category | Dynamic Programming |
| Subcategory | String / Top-Down |
| Complexity (Time) | O(mn) |
| Complexity (Space) | O(mn) for memoization |
| Input | Two strings |
| Output | Minimum edit operations |

## Overview

This is the **top-down (memoization)** approach to the Edit Distance (Levenshtein Distance) problem. It uses recursive function calls with caching to avoid recomputation, starting from the beginning of both strings and working forward.

## Mathematical Foundation

### Recurrence Relation

Let $d(i, j)$ = minimum operations to convert $word1[i:]$ to $word2[j:]$:

$$d(i, j) = \begin{cases}
|word2| - j & \text{if } i \geq |word1| \\
|word1| - i & \text{if } j \geq |word2| \\
d(i+1, j+1) & \text{if } word1[i] = word2[j] \\
1 + \min(d(i+1,j), d(i,j+1), d(i+1,j+1)) & \text{otherwise}
\end{cases}$$

### Operations

1. **Delete** from word1: $d(i+1, j) + 1$
2. **Insert** into word1: $d(i, j+1) + 1$
3. **Replace** in word1: $d(i+1, j+1) + 1$

## Algorithm

```
EDIT-DISTANCE-TOP-DOWN(word1, word2):
    memo = empty cache
    
    function min_distance(i, j):
        if (i, j) in memo:
            return memo[(i, j)]
        
        if i >= length(word1):
            return length(word2) - j
        if j >= length(word2):
            return length(word1) - i
        
        if word1[i] == word2[j]:
            result = min_distance(i+1, j+1)
        else:
            delete = 1 + min_distance(i+1, j)
            insert = 1 + min_distance(i, j+1)
            replace = 1 + min_distance(i+1, j+1)
            result = min(delete, insert, replace)
        
        memo[(i, j)] = result
        return result
    
    return min_distance(0, 0)
```

## Implementation (from repository)

```python
import functools


def min_distance_up_bottom(word1: str, word2: str) -> int:
    """
    >>> min_distance_up_bottom("intention", "execution")
    5
    >>> min_distance_up_bottom("intention", "")
    9
    >>> min_distance_up_bottom("", "")
    0
    >>> min_distance_up_bottom("zooicoarchaeologist", "zoologist")
    10
    """
    len_word1 = len(word1)
    len_word2 = len(word2)

    @functools.cache
    def min_distance(index1: int, index2: int) -> int:
        # if first word index overflows - delete all from the second word
        if index1 >= len_word1:
            return len_word2 - index2
        # if second word index overflows - delete all from the first word
        if index2 >= len_word2:
            return len_word1 - index1
        diff = int(word1[index1] != word2[index2])  # current letters not identical
        return min(
            1 + min_distance(index1 + 1, index2),
            1 + min_distance(index1, index2 + 1),
            diff + min_distance(index1 + 1, index2 + 1),
        )

    return min_distance(0, 0)
```

## Comparison: Top-Down vs Bottom-Up

| Aspect | Top-Down | Bottom-Up |
|--------|----------|-----------|
| Direction | Forward (0→n) | Backward (n→0) |
| Recursion | Yes | No |
| Cache | Hash map | 2D array |
| Lazy | Computes only needed | Computes all |
| Stack | May overflow | None |

## See Also

- [Edit Distance (Bottom-Up)](edit_distance.md) - Tabulation approach
- [Longest Common Subsequence](longest_common_subsequence.md) - Related problem
