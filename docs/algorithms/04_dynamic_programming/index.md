# Dynamic Programming Algorithms

This directory contains comprehensive documentation for dynamic programming algorithms implemented in the TheAlgorithms/Python repository.

## Overview

Dynamic Programming (DP) is an algorithmic technique for solving complex problems by breaking them down into simpler subproblems. It stores the results of subproblems to avoid redundant calculations, achieving optimal time complexity.

## Key Concepts

### Optimal Substructure
A problem has **optimal substructure** if an optimal solution can be constructed from optimal solutions of its subproblems.

### Overlapping Subproblems
A problem has **overlapping subproblems** if the recursive algorithm visits the same subproblems repeatedly.

### Approaches
- **Top-Down (Memoization)**: Recursive with caching
- **Bottom-Up (Tabulation)**: Iterative with table filling

## Algorithm Categories

### Classic DP Problems
| Algorithm | Time | Space | Problem Type |
|-----------|------|-------|--------------|
| [Fibonacci](fibonacci.md) | O(n) | O(n) | Sequence |
| [Climbing Stairs](climbing_stairs.md) | O(n) | O(1) | Counting paths |
| [Catalan Numbers](catalan_numbers.md) | O(n²) | O(n) | Combinatorics |
| [Factorial](factorial.md) | O(n) | O(n) | Mathematical |

### Sequence Algorithms
| Algorithm | Time | Space | Problem Type |
|-----------|------|-------|--------------|
| [Longest Common Subsequence](longest_common_subsequence.md) | O(mn) | O(mn) | String matching |
| [Longest Common Substring](longest_common_substring.md) | O(mn) | O(mn) | String matching |
| [Longest Increasing Subsequence](longest_increasing_subsequence.md) | O(n²) | O(n) | Array |
| [Longest Palindromic Subsequence](longest_palindromic_subsequence.md) | O(n²) | O(n²) | String |
| [Edit Distance](edit_distance.md) | O(mn) | O(mn) | String transformation |

### Optimization Problems
| Algorithm | Time | Space | Problem Type |
|-----------|------|-------|--------------|
| [0/1 Knapsack](knapsack.md) | O(nW) | O(nW) | Selection |
| [Coin Change](minimum_coin_change.md) | O(nW) | O(W) | Counting/Min |
| [Rod Cutting](rod_cutting.md) | O(n²) | O(n) | Optimization |
| [Matrix Chain Multiplication](matrix_chain_multiplication.md) | O(n³) | O(n²) | Optimization |

### Array Problems
| Algorithm | Time | Space | Problem Type |
|-----------|------|-------|--------------|
| [Maximum Subarray Sum](max_subarray_sum.md) | O(n) | O(1) | Kadane's algorithm |
| [Maximum Product Subarray](max_product_subarray.md) | O(n) | O(1) | Array |
| [Minimum Path Sum](minimum_cost_path.md) | O(mn) | O(n) | Grid traversal |
| [Trapped Water](trapped_water.md) | O(n) | O(n) | Two pointers/DP |

### Partitioning Problems
| Algorithm | Time | Space | Problem Type |
|-----------|------|-------|--------------|
| [Subset Sum](sum_of_subset.md) | O(nW) | O(W) | Subset selection |
| [Partition Equal Subset](minimum_partition.md) | O(nS) | O(S) | Partitioning |
| [Palindrome Partitioning](palindrome_partitioning.md) | O(n²) | O(n²) | String |

### Pattern Matching
| Algorithm | Time | Space | Problem Type |
|-----------|------|-------|--------------|
| [Regex Matching](regex_match.md) | O(mn) | O(mn) | Pattern matching |
| [Wildcard Matching](wildcard_matching.md) | O(mn) | O(mn) | Pattern matching |
| [Word Break](word_break.md) | O(n²) | O(n) | String segmentation |

### Advanced DP
| Algorithm | Time | Space | Problem Type |
|-----------|------|-------|--------------|
| [Bitmask DP](bitmask.md) | O(n·2ⁿ) | O(2ⁿ) | State compression |
| [Optimal BST](optimal_binary_search_tree.md) | O(n³) | O(n²) | Tree optimization |
| [Viterbi Algorithm](viterbi.md) | O(TK²) | O(TK) | HMM decoding |

## Problem Selection Guide

```
Need to solve optimization problem?
├── Sequence/String related?
│   ├── Compare two strings? → LCS, Edit Distance
│   ├── Single string palindrome? → Longest Palindromic Subsequence
│   └── Pattern matching? → Regex/Wildcard Matching
├── Selection/Counting?
│   ├── Item selection with weight? → Knapsack
│   ├── Coin combinations? → Coin Change
│   └── Subset selection? → Subset Sum
├── Array optimization?
│   ├── Maximum sum? → Kadane's Algorithm
│   ├── Grid path? → Minimum Path Sum
│   └── Product? → Max Product Subarray
└── Other?
    ├── Matrix multiplication order? → Matrix Chain
    ├── Combinatorics? → Catalan Numbers
    └── State machine? → Bitmask DP
```

## DP Problem-Solving Template

### Step 1: Define State
```python
# dp[i] represents the optimal solution for subproblem i
# dp[i][j] for 2D problems
```

### Step 2: Define Recurrence Relation
```python
# dp[i] = f(dp[i-1], dp[i-2], ...)
# dp[i][j] = f(dp[i-1][j], dp[i][j-1], ...)
```

### Step 3: Initialize Base Cases
```python
# dp[0] = base_value
# dp[0][j] = ..., dp[i][0] = ...
```

### Step 4: Determine Iteration Order
```python
# Usually from smaller to larger subproblems
for i in range(1, n + 1):
    for j in range(1, m + 1):
        # compute dp[i][j]
```

### Step 5: Return Result
```python
return dp[n]  # or dp[n][m]
```

## Complexity Comparison

| Problem Type | Brute Force | DP Solution |
|--------------|-------------|-------------|
| Fibonacci | O(2ⁿ) | O(n) |
| LCS | O(2^(m+n)) | O(mn) |
| 0/1 Knapsack | O(2ⁿ) | O(nW) |
| Matrix Chain | O(4ⁿ/n^1.5) | O(n³) |
| Edit Distance | O(3^max(m,n)) | O(mn) |

## Learning Path

### Beginner
1. [Fibonacci](fibonacci.md) - Basic memoization
2. [Climbing Stairs](climbing_stairs.md) - 1D DP
3. [Maximum Subarray](max_subarray_sum.md) - Kadane's algorithm

### Intermediate
4. [Longest Common Subsequence](longest_common_subsequence.md) - 2D DP
5. [Knapsack](knapsack.md) - Selection problems
6. [Edit Distance](edit_distance.md) - String DP
7. [Coin Change](minimum_coin_change.md) - Unbounded knapsack

### Advanced
8. [Matrix Chain Multiplication](matrix_chain_multiplication.md) - Interval DP
9. [Optimal BST](optimal_binary_search_tree.md) - Tree DP
10. [Bitmask DP](bitmask.md) - State compression

## See Also

- [Graphs](../03_graphs/index.md) - DP on graphs (shortest paths)
- [Searching](../02_searching/index.md) - Binary search optimization
- [Mathematics](../08_mathematics/index.md) - Combinatorics
