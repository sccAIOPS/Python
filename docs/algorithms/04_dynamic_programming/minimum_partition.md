# Minimum Partition

| Property | Value |
|----------|-------|
| Category | Dynamic Programming |
| Subcategory | Subset Sum / Partition |
| Complexity (Time) | O(n × S) where S = sum |
| Complexity (Space) | O(n × S) |
| Input | Array of integers |
| Output | Minimum difference between two subset sums |

## Overview

The **Minimum Partition** problem divides a set of numbers into two subsets such that the absolute difference between their sums is minimized. This is a variation of the Subset Sum problem.

## Mathematical Foundation

### Problem Definition

Given set $S = \{a_1, a_2, ..., a_n\}$, partition into $S_1$ and $S_2$ such that:

$$\text{minimize } |sum(S_1) - sum(S_2)|$$

### Key Insight

If total sum is $T$ and one subset has sum $s$:
- Other subset has sum $T - s$
- Difference = $|s - (T - s)| = |2s - T|$

**Goal**: Find $s$ closest to $T/2$ that is achievable.

### Recurrence

Let $dp[i][j]$ = true if sum $j$ is achievable using first $i$ elements:

$$dp[i][j] = dp[i-1][j] \lor dp[i-1][j - a_i]$$

## Algorithm

```
MINIMUM-PARTITION(numbers):
    n = length(numbers)
    S = sum(numbers)
    
    // dp[i][j] = can we get sum j using first i numbers?
    dp = matrix (n+1) × (S+1), all false
    
    // Base: sum 0 is always achievable
    for i from 0 to n:
        dp[i][0] = true
    
    // Fill DP table
    for i from 1 to n:
        for j from 1 to S:
            dp[i][j] = dp[i-1][j]
            if numbers[i-1] <= j:
                dp[i][j] = dp[i][j] OR dp[i-1][j - numbers[i-1]]
    
    // Find largest achievable sum <= S/2
    for j from S/2 down to 0:
        if dp[n][j] == true:
            return S - 2*j  // Minimum difference
```

## Visual Representation

### Example: [1, 2, 3, 4, 5]

```
Total sum = 15
Target = 15/2 = 7.5

Achievable sums (subset of first k elements):
k=1: {0, 1}
k=2: {0, 1, 2, 3}
k=3: {0, 1, 2, 3, 4, 5, 6}
k=4: {0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
k=5: {0, 1, ..., 15}

Largest sum ≤ 7: 7 (achievable with {3, 4} or {2, 5} or {1, 2, 4})
Other subset sum = 15 - 7 = 8
Minimum difference = |8 - 7| = 1
```

### DP Table Visualization

```
numbers = [1, 2, 3, 4, 5], sum = 15

       j:  0  1  2  3  4  5  6  7 ... 15
    i=0:   T  F  F  F  F  F  F  F     F
    i=1:   T  T  F  F  F  F  F  F     F
    i=2:   T  T  T  T  F  F  F  F     F
    i=3:   T  T  T  T  T  T  T  F     F
    i=4:   T  T  T  T  T  T  T  T     F
    i=5:   T  T  T  T  T  T  T  T     T

Look for rightmost T in bottom row ≤ 7: j=7
diff = 15 - 2×7 = 1
```

## Implementation (from repository)

```python
def find_min(numbers: list[int]) -> int:
    """
    >>> find_min([1, 2, 3, 4, 5])
    1
    >>> find_min([5, 5, 5, 5, 5])
    5
    >>> find_min([5, 5, 5, 5])
    0
    >>> find_min([3])
    3
    >>> find_min([])
    0
    >>> find_min([1, 2, 3, 4])
    0
    """
    n = len(numbers)
    s = sum(numbers)

    dp = [[False for x in range(s + 1)] for y in range(n + 1)]

    for i in range(n + 1):
        dp[i][0] = True

    for i in range(1, s + 1):
        dp[0][i] = False

    for i in range(1, n + 1):
        for j in range(1, s + 1):
            dp[i][j] = dp[i - 1][j]

            if numbers[i - 1] <= j:
                dp[i][j] = dp[i][j] or dp[i - 1][j - numbers[i - 1]]

    for j in range(int(s / 2), -1, -1):
        if dp[n][j] is True:
            diff = s - 2 * j
            break

    return diff
```

## Real-World Applications

### 1. Team Balancing

```python
def balance_teams(
    player_skills: list[int]
) -> dict:
    """
    Divide players into two teams with balanced total skill.
    
    >>> result = balance_teams([85, 90, 75, 80, 95, 70])
    >>> result['skill_difference'] <= 5
    True
    """
    n = len(player_skills)
    s = sum(player_skills)
    
    # DP with tracking
    dp = [[False] * (s + 1) for _ in range(n + 1)]
    for i in range(n + 1):
        dp[i][0] = True
    
    for i in range(1, n + 1):
        for j in range(s + 1):
            dp[i][j] = dp[i-1][j]
            if player_skills[i-1] <= j:
                dp[i][j] = dp[i][j] or dp[i-1][j - player_skills[i-1]]
    
    # Find best sum
    best_sum = 0
    for j in range(s // 2, -1, -1):
        if dp[n][j]:
            best_sum = j
            break
    
    # Reconstruct team
    team1 = []
    j = best_sum
    for i in range(n, 0, -1):
        if j >= player_skills[i-1] and dp[i-1][j - player_skills[i-1]]:
            team1.append(i-1)
            j -= player_skills[i-1]
    
    team2 = [i for i in range(n) if i not in team1]
    
    return {
        'team1': team1,
        'team2': team2,
        'team1_skill': best_sum,
        'team2_skill': s - best_sum,
        'skill_difference': abs(s - 2 * best_sum)
    }
```

### 2. Resource Allocation

```python
def split_workload(
    tasks: list[int]
) -> dict:
    """
    Split tasks between two processors for balanced load.
    
    >>> result = split_workload([10, 20, 30, 40, 50])
    >>> result['imbalance'] <= 10
    True
    """
    n = len(tasks)
    total = sum(tasks)
    
    # Space-optimized DP
    dp = [False] * (total + 1)
    dp[0] = True
    
    for task in tasks:
        # Iterate backwards to avoid using same task twice
        for j in range(total, task - 1, -1):
            if dp[j - task]:
                dp[j] = True
    
    # Find minimum difference
    min_diff = total
    for j in range(total // 2, -1, -1):
        if dp[j]:
            min_diff = total - 2 * j
            break
    
    return {
        'total_workload': total,
        'balanced_split': (total + min_diff) // 2,
        'imbalance': min_diff
    }
```

## Variations

### Equal Partition (Can Split Exactly?)

```python
def can_partition(nums: list[int]) -> bool:
    """
    Check if array can be partitioned into two equal sum subsets.
    
    >>> can_partition([1, 5, 11, 5])
    True
    """
    total = sum(nums)
    if total % 2 != 0:
        return False
    
    target = total // 2
    dp = [False] * (target + 1)
    dp[0] = True
    
    for num in nums:
        for j in range(target, num - 1, -1):
            dp[j] = dp[j] or dp[j - num]
    
    return dp[target]
```

## Common Pitfalls

1. **Negative numbers**: Current implementation doesn't handle negatives
2. **Integer overflow**: Sum can be large
3. **Space optimization**: Can use 1D array

## See Also

- [Subset Sum](subset_sum.md) - Related problem
- [0/1 Knapsack](knapsack.md) - Similar DP structure
- [Partition Equal Subset Sum](partition_equal_subset.md) - Special case
