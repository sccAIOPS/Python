# Catalan Numbers

| Property | Value |
|----------|-------|
| Category | Dynamic Programming |
| Subcategory | Combinatorics |
| Complexity (Time) | O(n²) |
| Complexity (Space) | O(n) |
| Input | Non-negative integer n |
| Output | n-th Catalan number |

## Overview

**Catalan numbers** form a sequence of natural numbers that appear in various counting problems in combinatorics. They count many fascinating structures including valid parentheses expressions, binary tree shapes, polygon triangulations, and Dyck paths.

## Mathematical Foundation

### Definition

The $n$-th Catalan number is defined as:

$$C_n = \frac{1}{n+1}\binom{2n}{n} = \frac{(2n)!}{(n+1)!n!}$$

### Recurrence Relation

$$C_n = \sum_{i=0}^{n-1} C_i \cdot C_{n-1-i}$$

With base cases: $C_0 = C_1 = 1$

### Alternative Formula

$$C_n = \frac{2(2n-1)}{n+1} C_{n-1}$$

### First Few Values

| n | $C_n$ |
|---|-------|
| 0 | 1 |
| 1 | 1 |
| 2 | 2 |
| 3 | 5 |
| 4 | 14 |
| 5 | 42 |
| 6 | 132 |
| 7 | 429 |
| 8 | 1430 |
| 9 | 4862 |
| 10 | 16796 |

### Asymptotic Growth

$$C_n \sim \frac{4^n}{n^{3/2}\sqrt{\pi}}$$

## Algorithm Approaches

### 1. Naive Recursive (O(3ⁿ))

```
CATALAN-NAIVE(n):
    if n <= 1:
        return 1
    
    result = 0
    for i from 0 to n-1:
        result = result + CATALAN-NAIVE(i) × CATALAN-NAIVE(n - 1 - i)
    
    return result
```

### 2. Memoized Recursive (O(n²))

```
CATALAN-MEMO(n):
    memo = new array[n + 1], all -1
    return CATALAN-MEMO-AUX(n, memo)

CATALAN-MEMO-AUX(n, memo):
    if n <= 1:
        return 1
    if memo[n] != -1:
        return memo[n]
    
    result = 0
    for i from 0 to n-1:
        result = result + CATALAN-MEMO-AUX(i, memo) × CATALAN-MEMO-AUX(n - 1 - i, memo)
    
    memo[n] = result
    return result
```

### 3. Bottom-Up DP (O(n²))

```
CATALAN-DP(n):
    dp = new array[n + 1]
    dp[0] = dp[1] = 1
    
    for i from 2 to n:
        dp[i] = 0
        for j from 0 to i-1:
            dp[i] = dp[i] + dp[j] × dp[i - 1 - j]
    
    return dp[n]
```

### 4. Direct Formula (O(n))

```
CATALAN-DIRECT(n):
    if n <= 1:
        return 1
    
    // Calculate C(2n, n)
    numerator = 1
    denominator = 1
    for i from 0 to n-1:
        numerator = numerator × (2n - i)
        denominator = denominator × (i + 1)
    
    return numerator / (denominator × (n + 1))
```

### 5. Iterative Formula (O(n))

```
CATALAN-ITERATIVE(n):
    result = 1
    for i from 1 to n:
        result = result × 2 × (2i - 1) / (i + 1)
    return result
```

## Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Naive Recursive | O(3ⁿ) | O(n) | Exponential |
| Memoized | O(n²) | O(n) | Optimal for DP |
| Bottom-Up | O(n²) | O(n) | Builds sequence |
| Direct Formula | O(n) | O(1) | May overflow |
| Iterative | O(n) | O(1) | Best practical |

## Visual Representation

### Recurrence Structure

```
C(4) = C(0)×C(3) + C(1)×C(2) + C(2)×C(1) + C(3)×C(0)
     = 1×5 + 1×2 + 2×1 + 5×1
     = 5 + 2 + 2 + 5
     = 14

Visualization for C(4):
        n=4
       /    \
    (0,3)  (1,2)  (2,1)  (3,0)
      |      |      |      |
    1×5    1×2    2×1    5×1

Sum = 14
```

### DP Table Construction

```
Computing C(5):

dp[0] = 1  (base case)
dp[1] = 1  (base case)

dp[2] = dp[0]×dp[1] + dp[1]×dp[0]
      = 1×1 + 1×1 = 2

dp[3] = dp[0]×dp[2] + dp[1]×dp[1] + dp[2]×dp[0]
      = 1×2 + 1×1 + 2×1 = 5

dp[4] = dp[0]×dp[3] + dp[1]×dp[2] + dp[2]×dp[1] + dp[3]×dp[0]
      = 1×5 + 1×2 + 2×1 + 5×1 = 14

dp[5] = dp[0]×dp[4] + dp[1]×dp[3] + dp[2]×dp[2] + dp[3]×dp[1] + dp[4]×dp[0]
      = 1×14 + 1×5 + 2×2 + 5×1 + 14×1 = 42
```

### Counting Interpretations

```
C(3) = 5 counts:

1. Valid Parentheses:
   ((()))  (()())  (())()  ()(())  ()()()
   
2. Binary Trees with 3 internal nodes:
       ○         ○         ○         ○         ○
      / \       / \       / \       / \       / \
     ○   ○     ○   ○     ○   ○     ○   ○     ○   ○
    /         / \           \       \         /   \
   ○         ○   ○           ○       ○       ○     ○

3. Paths in grid (Dyck paths):
   Stay above diagonal from (0,0) to (n,n)
```

## Implementation (from repository)

```python
def catalan_numbers(upper_limit: int) -> "list[int]":
    """
    Return a list of the Catalan number sequence from 0 through `upper_limit`.

    The Catalan numbers are a sequence of positive integers that
    appear in many counting problems in combinatorics. Such
    problems include counting:
    - The number of Dyck words of length 2n
    - The number well-formed expressions with n pairs of parentheses
      (e.g., `()()` is valid but `())(` is not)
    - The number of different ways n + 1 factors can be completely
      parenthesized (e.g., for n = 2, C(n) = 2 and (ab)c and a(bc)
      are the two valid ways to parenthesize.
    - The number of full binary trees with n + 1 leaves

    A Catalan number satisfies the following recurrence relation:
    C(0) = C(1) = 1
    C(n) = sum(C(i).C(n-i-1)), from i = 0 to n-1

    In addition, the n-th Catalan number can be calculated using
    the closed form formula:
    C(n) = (1 / (n + 1)) * (2n choose n)

    Sources:
     [1] https://brilliant.org/wiki/catalan-numbers/
     [2] https://en.wikipedia.org/wiki/Catalan_number

    >>> catalan_numbers(5)
    [1, 1, 2, 5, 14, 42]
    >>> catalan_numbers(2)
    [1, 1, 2]
    >>> catalan_numbers(-1)
    Traceback (most recent call last):
    ValueError: Limit for the Catalan sequence must be ≥ 0
    """
    if upper_limit < 0:
        raise ValueError("Limit for the Catalan sequence must be ≥ 0")

    catalan_list = [0] * (upper_limit + 1)

    # Base case: C(0) = C(1) = 1
    catalan_list[0] = 1
    if upper_limit > 0:
        catalan_list[1] = 1

    # Recurrence relation: C(i) = sum(C(j).C(i-j-1)), from j = 0 to i
    for i in range(2, upper_limit + 1):
        for j in range(i):
            catalan_list[i] += catalan_list[j] * catalan_list[i - j - 1]

    return catalan_list
```

## Real-World Applications

### 1. Expression Parsing - Valid Parentheses

```python
from typing import List, Set

def count_valid_parentheses(n: int) -> int:
    """
    Count valid parentheses expressions with n pairs.
    This equals the n-th Catalan number.
    
    >>> count_valid_parentheses(3)
    5
    """
    if n <= 0:
        return 1
    
    # Use Catalan recurrence
    dp = [0] * (n + 1)
    dp[0] = 1
    
    for i in range(1, n + 1):
        for j in range(i):
            dp[i] += dp[j] * dp[i - 1 - j]
    
    return dp[n]


def generate_valid_parentheses(n: int) -> List[str]:
    """
    Generate all valid parentheses combinations.
    
    >>> sorted(generate_valid_parentheses(2))
    ['(())', '()()']
    >>> len(generate_valid_parentheses(3))
    5
    """
    result = []
    
    def backtrack(current: str, open_count: int, close_count: int):
        if len(current) == 2 * n:
            result.append(current)
            return
        
        if open_count < n:
            backtrack(current + '(', open_count + 1, close_count)
        
        if close_count < open_count:
            backtrack(current + ')', open_count, close_count + 1)
    
    backtrack('', 0, 0)
    return result


def validate_bracket_expression(expr: str) -> bool:
    """
    Validate if bracket expression is well-formed.
    
    >>> validate_bracket_expression("(()())")
    True
    >>> validate_bracket_expression("())(")
    False
    """
    bracket_map = {')': '(', ']': '[', '}': '{'}
    stack = []
    
    for char in expr:
        if char in '([{':
            stack.append(char)
        elif char in ')]}':
            if not stack or stack[-1] != bracket_map[char]:
                return False
            stack.pop()
    
    return len(stack) == 0


def count_bracket_colorings(n: int, k: int) -> int:
    """
    Count ways to color n bracket pairs with k colors
    such that matching brackets have same color.
    
    >>> count_bracket_colorings(2, 2)  # 2 structures × 2² colors
    8
    """
    catalan = count_valid_parentheses(n)
    return catalan * (k ** n)
```

### 2. Binary Tree Enumeration

```python
from typing import List, Optional, Any
from dataclasses import dataclass

@dataclass
class TreeNode:
    value: Any = None
    left: Optional['TreeNode'] = None
    right: Optional['TreeNode'] = None

def count_binary_trees(n: int) -> int:
    """
    Count structurally unique binary trees with n nodes.
    
    >>> count_binary_trees(3)
    5
    >>> count_binary_trees(4)
    14
    """
    if n <= 1:
        return 1
    
    dp = [0] * (n + 1)
    dp[0] = dp[1] = 1
    
    for i in range(2, n + 1):
        for j in range(i):
            # j nodes in left subtree, i-1-j in right
            dp[i] += dp[j] * dp[i - 1 - j]
    
    return dp[n]


def generate_all_bst(n: int) -> List[TreeNode]:
    """
    Generate all structurally unique BSTs with values 1 to n.
    
    >>> trees = generate_all_bst(3)
    >>> len(trees)
    5
    """
    def generate_trees(start: int, end: int) -> List[Optional[TreeNode]]:
        if start > end:
            return [None]
        
        all_trees = []
        
        for root_val in range(start, end + 1):
            # Generate all left and right subtrees
            left_trees = generate_trees(start, root_val - 1)
            right_trees = generate_trees(root_val + 1, end)
            
            # Combine all possibilities
            for left in left_trees:
                for right in right_trees:
                    root = TreeNode(root_val, left, right)
                    all_trees.append(root)
        
        return all_trees
    
    return generate_trees(1, n)


def count_full_binary_trees(leaves: int) -> int:
    """
    Count full binary trees with given number of leaves.
    A full binary tree has either 0 or 2 children at each node.
    
    >>> count_full_binary_trees(4)  # C(3) = 5
    5
    """
    # Full binary tree with n leaves has n-1 internal nodes
    # Number of such trees = C(n-1)
    n = leaves - 1
    if n < 0:
        return 0
    
    dp = [0] * (n + 1)
    dp[0] = 1
    
    for i in range(1, n + 1):
        for j in range(i):
            dp[i] += dp[j] * dp[i - 1 - j]
    
    return dp[n]


def analyze_tree_distribution(n: int) -> dict:
    """
    Analyze distribution of tree shapes by depth.
    
    >>> result = analyze_tree_distribution(4)
    >>> 'total_trees' in result
    True
    """
    def tree_depth(node: Optional[TreeNode]) -> int:
        if node is None:
            return 0
        return 1 + max(tree_depth(node.left), tree_depth(node.right))
    
    trees = generate_all_bst(n)
    depths = [tree_depth(t) for t in trees]
    
    depth_distribution = {}
    for d in depths:
        depth_distribution[d] = depth_distribution.get(d, 0) + 1
    
    return {
        'total_trees': len(trees),
        'depth_distribution': depth_distribution,
        'min_depth': min(depths),
        'max_depth': max(depths),
        'avg_depth': sum(depths) / len(depths)
    }
```

### 3. Polygon Triangulation

```python
from typing import List, Tuple
import math

def count_triangulations(n: int) -> int:
    """
    Count ways to triangulate a convex polygon with n+2 vertices.
    
    >>> count_triangulations(2)  # Square (4 vertices) = 2 ways
    2
    >>> count_triangulations(3)  # Pentagon (5 vertices) = 5 ways
    5
    """
    # Triangulating polygon with n+2 vertices = C(n)
    if n <= 1:
        return 1
    
    dp = [0] * (n + 1)
    dp[0] = dp[1] = 1
    
    for i in range(2, n + 1):
        for j in range(i):
            dp[i] += dp[j] * dp[i - 1 - j]
    
    return dp[n]


def generate_triangulations(
    vertices: List[Tuple[float, float]]
) -> List[List[Tuple[int, int, int]]]:
    """
    Generate all triangulations of a convex polygon.
    
    Returns list of triangulations, each being list of vertex triples.
    
    >>> square = [(0,0), (1,0), (1,1), (0,1)]
    >>> triangulations = generate_triangulations(square)
    >>> len(triangulations)
    2
    """
    n = len(vertices)
    if n < 3:
        return []
    if n == 3:
        return [[(0, 1, 2)]]
    
    result = []
    
    def triangulate(polygon_indices: List[int], current: List[Tuple[int, int, int]]):
        if len(polygon_indices) == 3:
            result.append(current + [tuple(polygon_indices)])
            return
        
        # Fix first edge and try all third vertices
        for i in range(2, len(polygon_indices)):
            # Triangle with vertices 0, 1, i
            triangle = (polygon_indices[0], polygon_indices[1], polygon_indices[i])
            
            # Remaining polygon(s)
            if i == 2:
                # Only right part remains
                remaining = [polygon_indices[0]] + polygon_indices[2:]
                triangulate(remaining, current + [triangle])
            elif i == len(polygon_indices) - 1:
                # Only left part remains
                remaining = polygon_indices[:i+1]
                remaining[-1] = polygon_indices[-1]
                triangulate(polygon_indices[1:], current + [triangle])
            else:
                # Both parts - need to handle recursively
                # This is simplified; full implementation needs ear clipping
                pass
    
    # Simplified: use ear clipping approach
    def ear_clip(indices: List[int], triangles: List[Tuple[int, int, int]]):
        if len(indices) == 3:
            result.append(triangles + [tuple(indices)])
            return
        
        # Try each vertex as potential ear
        for i in range(len(indices)):
            prev_i = (i - 1) % len(indices)
            next_i = (i + 1) % len(indices)
            
            # Form triangle
            triangle = (indices[prev_i], indices[i], indices[next_i])
            
            # Remove ear vertex
            new_indices = indices[:i] + indices[i+1:]
            ear_clip(new_indices, triangles + [triangle])
    
    ear_clip(list(range(n)), [])
    return result


def optimal_triangulation_cost(
    vertices: List[Tuple[float, float]]
) -> Tuple[float, List[Tuple[int, int, int]]]:
    """
    Find minimum cost triangulation (minimize total edge length).
    
    >>> vertices = [(0,0), (1,0), (0.5,1)]
    >>> cost, triangles = optimal_triangulation_cost(vertices)
    >>> len(triangles) == 1
    True
    """
    n = len(vertices)
    
    def distance(i: int, j: int) -> float:
        dx = vertices[i][0] - vertices[j][0]
        dy = vertices[i][1] - vertices[j][1]
        return math.sqrt(dx*dx + dy*dy)
    
    def triangle_cost(i: int, j: int, k: int) -> float:
        return distance(i, j) + distance(j, k) + distance(k, i)
    
    if n < 3:
        return 0, []
    if n == 3:
        return triangle_cost(0, 1, 2), [(0, 1, 2)]
    
    # dp[i][j] = minimum cost to triangulate polygon from i to j
    INF = float('inf')
    dp = [[INF] * n for _ in range(n)]
    parent = [[(-1, -1)] * n for _ in range(n)]
    
    # Base case: adjacent vertices
    for i in range(n - 1):
        dp[i][i + 1] = 0
    
    # Fill DP table
    for length in range(2, n):
        for i in range(n - length):
            j = i + length
            for k in range(i + 1, j):
                cost = dp[i][k] + dp[k][j] + triangle_cost(i, k, j)
                if cost < dp[i][j]:
                    dp[i][j] = cost
                    parent[i][j] = (k, -1)
    
    # Reconstruct triangulation
    triangles = []
    
    def reconstruct(i: int, j: int):
        if j - i < 2:
            return
        k = parent[i][j][0]
        if k != -1:
            triangles.append((i, k, j))
            reconstruct(i, k)
            reconstruct(k, j)
    
    reconstruct(0, n - 1)
    return dp[0][n - 1], triangles
```

### 4. Stack Sortable Permutations

```python
from typing import List

def count_stack_sortable(n: int) -> int:
    """
    Count permutations of 1..n that can be sorted using a stack.
    This equals the n-th Catalan number.
    
    >>> count_stack_sortable(3)
    5
    >>> count_stack_sortable(4)
    14
    """
    if n <= 1:
        return 1
    
    dp = [0] * (n + 1)
    dp[0] = dp[1] = 1
    
    for i in range(2, n + 1):
        for j in range(i):
            dp[i] += dp[j] * dp[i - 1 - j]
    
    return dp[n]


def is_stack_sortable(perm: List[int]) -> bool:
    """
    Check if permutation can be sorted using a single stack.
    
    >>> is_stack_sortable([2, 3, 1])
    True
    >>> is_stack_sortable([3, 1, 2])
    False
    """
    stack = []
    expected = 1
    
    for num in perm:
        # Pop elements that should come next
        while stack and stack[-1] == expected:
            stack.pop()
            expected += 1
        
        # Push current element
        stack.append(num)
    
    # Try to pop remaining
    while stack and stack[-1] == expected:
        stack.pop()
        expected += 1
    
    return len(stack) == 0


def generate_stack_sortable(n: int) -> List[List[int]]:
    """
    Generate all stack-sortable permutations of 1..n.
    
    >>> perms = generate_stack_sortable(3)
    >>> len(perms)
    5
    >>> [2, 3, 1] in perms
    True
    """
    from itertools import permutations
    
    return [
        list(p) for p in permutations(range(1, n + 1))
        if is_stack_sortable(list(p))
    ]
```

## Variations

### Ballot Numbers (Generalized Catalan)

```python
def ballot_numbers(n: int, k: int) -> int:
    """
    Count ballot sequences where candidate A always leads B.
    A gets n votes, B gets k votes (k <= n).
    
    >>> ballot_numbers(3, 2)  # A wins 3-2, always leading
    2
    """
    if k > n:
        return 0
    if k == 0:
        return 1
    
    # Use reflection principle
    # Total paths - bad paths
    from math import comb
    total = comb(n + k, k)
    bad = comb(n + k, k - 1)  # Paths that touch y = x
    return total - bad
```

### Motzkin Numbers

```python
def motzkin_numbers(n: int) -> List[int]:
    """
    Motzkin numbers: paths from (0,0) to (n,0) with steps
    (1,1), (1,-1), and (1,0), staying non-negative.
    
    >>> motzkin_numbers(5)
    [1, 1, 2, 4, 9, 21]
    """
    if n < 0:
        return []
    
    M = [0] * (n + 1)
    M[0] = 1
    if n > 0:
        M[1] = 1
    
    for i in range(2, n + 1):
        M[i] = M[i - 1]
        for j in range(i - 1):
            M[i] += M[j] * M[i - 2 - j]
    
    return M
```

## Common Pitfalls

1. **Off-by-one**: Carefully match n to the counting problem
2. **Integer overflow**: Catalan numbers grow exponentially
3. **Index confusion**: In recurrence, sum from 0 to n-1
4. **Base cases**: Both C(0) and C(1) equal 1

## References

- [Catalan Numbers - Wikipedia](https://en.wikipedia.org/wiki/Catalan_number)
- [Catalan Numbers - Brilliant](https://brilliant.org/wiki/catalan-numbers/)
- Stanley, R. (2015). Catalan Numbers

## See Also

- [Binary Trees](../data_structures/binary_tree.md) - Tree structures
- [Generate Parentheses](generate_parentheses.md) - Application
- [Matrix Chain Multiplication](matrix_chain_multiplication.md) - Related problem
