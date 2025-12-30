# Catalan Numbers

## Overview
- **Category**: Combinatorics
- **Complexity**: Time: O(n) | Space: O(1) or O(n)
- **Type**: Counting sequence
- **Source File**: [maths/catalan_number.py](../../../maths/catalan_number.py)

## 1. Mathematical Foundation

### 1.1 Definition

The nth Catalan number $C_n$ is defined as:

$$
C_n = \frac{1}{n+1}\binom{2n}{n} = \frac{(2n)!}{(n+1)!n!}
$$

### 1.2 First Few Values

| n | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 |
|---|---|---|---|---|---|---|---|---|---|---|---|
| $C_n$ | 1 | 1 | 2 | 5 | 14 | 42 | 132 | 429 | 1430 | 4862 | 16796 |

### 1.3 Recurrence Relations

**Direct recurrence:**
$$
C_n = \sum_{i=0}^{n-1} C_i \cdot C_{n-1-i}
$$

**Simple recurrence:**
$$
C_{n+1} = \frac{2(2n+1)}{n+2} C_n
$$

### 1.4 Generating Function

$$
C(x) = \sum_{n=0}^{\infty} C_n x^n = \frac{1 - \sqrt{1-4x}}{2x}
$$

### 1.5 Asymptotic Growth

$$
C_n \sim \frac{4^n}{n^{3/2}\sqrt{\pi}}
$$

## 2. Counting Interpretations

Catalan numbers count remarkably many combinatorial structures:

### 2.1 Balanced Parentheses

$C_n$ = number of ways to arrange $n$ pairs of matching parentheses.

**Example ($n=3$)**: 5 arrangements
- `((()))`
- `(()())`
- `(())()`
- `()(())`
- `()()()`

### 2.2 Binary Trees

$C_n$ = number of distinct binary trees with $n$ internal nodes.

### 2.3 Triangulations

$C_{n-2}$ = number of ways to triangulate a convex $n$-gon using non-crossing diagonals.

### 2.4 Dyck Paths

$C_n$ = number of paths from $(0,0)$ to $(2n,0)$ using up $(+1,+1)$ and down $(+1,-1)$ steps, never going below x-axis.

### 2.5 Mountain Ranges

$C_n$ = number of ways to draw $n$ non-intersecting peaks.

### 2.6 Non-Crossing Partitions

$C_n$ = number of non-crossing partitions of $\{1, 2, \ldots, n\}$.

### 2.7 Ballot Problem

$C_n$ = number of ways candidate A can lead throughout counting if both A and B get $n$ votes and A wins.

## 3. Derivation via Reflection Principle

### The Ballot Problem Approach

Count lattice paths from $(0,0)$ to $(n,n)$ that don't cross the diagonal:

- Total paths: $\binom{2n}{n}$
- Bad paths (cross diagonal): $\binom{2n}{n+1}$

$$
C_n = \binom{2n}{n} - \binom{2n}{n+1} = \frac{1}{n+1}\binom{2n}{n}
$$

## 4. Pseudocode

```
ALGORITHM CatalanNumber-Direct(n)
    INPUT: Non-negative integer n
    OUTPUT: nth Catalan number
    
    if n ≤ 1 then
        return 1
    
    result ← 0
    for i ← 0 to n-1 do
        result ← result + Catalan(i) × Catalan(n-1-i)
    
    return result

ALGORITHM CatalanNumber-Formula(n)
    INPUT: Non-negative integer n
    OUTPUT: nth Catalan number
    
    return BinomialCoefficient(2n, n) / (n + 1)

ALGORITHM CatalanNumber-Iterative(n)
    INPUT: Non-negative integer n
    OUTPUT: nth Catalan number
    
    C ← 1
    for i ← 0 to n-1 do
        C ← C × 2 × (2i + 1) / (i + 2)
    
    return C

ALGORITHM GenerateCatalanSequence(n)
    INPUT: Non-negative integer n
    OUTPUT: Array of first n+1 Catalan numbers
    
    C[0] ← 1
    for i ← 1 to n do
        C[i] ← 0
        for j ← 0 to i-1 do
            C[i] ← C[i] + C[j] × C[i-1-j]
    
    return C
```

## 5. Step-by-Step Example

### Example: Compute $C_4$

**Using convolution:**
$$
C_4 = C_0 C_3 + C_1 C_2 + C_2 C_1 + C_3 C_0
$$
$$
= 1 \cdot 5 + 1 \cdot 2 + 2 \cdot 1 + 5 \cdot 1 = 14
$$

**Using formula:**
$$
C_4 = \frac{1}{5}\binom{8}{4} = \frac{1}{5} \cdot 70 = 14
$$

**Verify with parentheses (4 pairs)**:
```
1. (((())))    8. (())(())
2. ((()()))    9. (())()()
3. ((())())   10. ()(())()
4. ((()))()   11. ()(()())
5. (()()())   12. ()((()))
6. (()(()))   13. ()()(())
7. (()())()   14. ()()()()
```

Count: 14 ✓

## 6. Complexity Analysis

| Method | Time | Space | Notes |
|--------|------|-------|-------|
| Direct Recursion | O(4^n) | O(n) | Exponential, avoid |
| Memoized Recursion | O(n²) | O(n) | Convolution |
| DP (Convolution) | O(n²) | O(n) | Build sequence |
| Formula | O(n) | O(1) | Single value |
| Iterative | O(n) | O(1) | Recurrence |

## 7. Visual Representation

### 7.1 Catalan Structure Tree

```
             C_4 = 14
           /    |    \
       C₀C₃   C₁C₂   C₂C₁   C₃C₀
        |      |       |      |
      1×5    1×2     2×1    5×1
        5  +   2   +   2  +   5  = 14
```

### 7.2 Dyck Paths for n=3

```
    /\
   /  \      /\        /\  /\      /\        /\
  /    \    /  \/\    /  \/  \    /  \  /\  /  \/\/\
━━━━━━━━  ━━━━━━━━  ━━━━━━━━━━  ━━━━━━━━━━  ━━━━━━━━
C_3 = 5 distinct Dyck paths
```

### 7.3 Binary Trees for n=3

```
    ○        ○        ○        ○        ○
   / \      / \        \      /        /
  ○   ○    ○   ○        ○    ○        ○
 /          \          / \    \        \
○            ○        ○   ○    ○        ○
                                        /
                                       ○

C_3 = 5 distinct binary trees with 3 internal nodes
```

## 8. Implementation

```python
from typing import List, Generator
from functools import lru_cache
import math


def catalan_formula(n: int) -> int:
    """
    Compute nth Catalan number using closed formula.
    
    C(n) = C(2n, n) / (n + 1)
    
    >>> catalan_formula(0)
    1
    >>> catalan_formula(5)
    42
    >>> catalan_formula(10)
    16796
    """
    if n < 0:
        return 0
    return math.comb(2 * n, n) // (n + 1)


def catalan_iterative(n: int) -> int:
    """
    Compute nth Catalan number iteratively.
    
    Uses recurrence: C(n+1) = C(n) * 2(2n+1) / (n+2)
    
    >>> catalan_iterative(5)
    42
    """
    if n < 0:
        return 0
    
    c = 1
    for i in range(n):
        c = c * 2 * (2 * i + 1) // (i + 2)
    
    return c


def catalan_dp(n: int) -> List[int]:
    """
    Generate first n+1 Catalan numbers using DP.
    
    Uses convolution: C(n) = Σ C(i) * C(n-1-i)
    
    >>> catalan_dp(5)
    [1, 1, 2, 5, 14, 42]
    """
    if n < 0:
        return []
    
    C = [0] * (n + 1)
    C[0] = 1
    
    for i in range(1, n + 1):
        for j in range(i):
            C[i] += C[j] * C[i - 1 - j]
    
    return C


@lru_cache(maxsize=None)
def catalan_recursive(n: int) -> int:
    """
    Compute nth Catalan number recursively with memoization.
    
    >>> catalan_recursive(5)
    42
    """
    if n <= 1:
        return 1
    
    result = 0
    for i in range(n):
        result += catalan_recursive(i) * catalan_recursive(n - 1 - i)
    
    return result


def catalan_mod(n: int, mod: int = 10**9 + 7) -> int:
    """
    Compute nth Catalan number modulo m.
    
    >>> catalan_mod(5)
    42
    >>> catalan_mod(1000)
    164750656
    """
    # Compute using formula: C(n) = C(2n, n) / (n+1)
    # Need modular inverse for division
    
    # Compute C(2n, n) mod m
    numerator = 1
    denominator = 1
    
    for i in range(n):
        numerator = (numerator * (2 * n - i)) % mod
        denominator = (denominator * (i + 1)) % mod
    
    # Multiply by modular inverse of (n+1)
    binom = (numerator * pow(denominator, mod - 2, mod)) % mod
    inv_n_plus_1 = pow(n + 1, mod - 2, mod)
    
    return (binom * inv_n_plus_1) % mod


def catalan_sequence_mod(n: int, mod: int = 10**9 + 7) -> List[int]:
    """
    Generate first n+1 Catalan numbers modulo m.
    
    >>> catalan_sequence_mod(5)
    [1, 1, 2, 5, 14, 42]
    """
    C = [0] * (n + 1)
    C[0] = 1
    
    for i in range(1, n + 1):
        for j in range(i):
            C[i] = (C[i] + C[j] * C[i - 1 - j]) % mod
    
    return C


def catalan_generator(mod: int = None) -> Generator[int, None, None]:
    """
    Generator for Catalan numbers.
    
    >>> gen = catalan_generator()
    >>> [next(gen) for _ in range(6)]
    [1, 1, 2, 5, 14, 42]
    """
    C = [1]  # C[0] = 1
    yield 1
    
    n = 1
    while True:
        # Compute C[n]
        c_n = 0
        for j in range(n):
            c_n += C[j] * C[n - 1 - j]
        
        if mod:
            c_n %= mod
        
        C.append(c_n)
        yield c_n
        n += 1


def count_balanced_parentheses(n: int) -> int:
    """
    Count balanced arrangements of n pairs of parentheses.
    
    >>> count_balanced_parentheses(3)
    5
    """
    return catalan_formula(n)


def count_binary_trees(n: int) -> int:
    """
    Count distinct binary trees with n nodes.
    
    >>> count_binary_trees(3)
    5
    """
    return catalan_formula(n)


def count_triangulations(n: int) -> int:
    """
    Count triangulations of convex n-gon.
    
    >>> count_triangulations(5)  # Pentagon
    5
    >>> count_triangulations(6)  # Hexagon
    14
    """
    if n < 3:
        return 0
    return catalan_formula(n - 2)


def generate_balanced_parentheses(n: int) -> List[str]:
    """
    Generate all balanced parentheses strings of length 2n.
    
    >>> generate_balanced_parentheses(2)
    ['(())', '()()']
    >>> len(generate_balanced_parentheses(3))
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


def count_dyck_paths(n: int) -> int:
    """
    Count Dyck paths from (0,0) to (2n, 0).
    
    >>> count_dyck_paths(3)
    5
    """
    return catalan_formula(n)


def count_non_crossing_partitions(n: int) -> int:
    """
    Count non-crossing partitions of {1, 2, ..., n}.
    
    >>> count_non_crossing_partitions(4)
    14
    """
    return catalan_formula(n)
```

## 9. Applications

### 9.1 Computer Science

| Application | Formula | Description |
|-------------|---------|-------------|
| Expression parsing | $C_n$ | Ways to parenthesize n+1 terms |
| BST shapes | $C_n$ | Distinct BSTs with n keys |
| Stack sortable | $C_n$ | Permutations sortable by stack |
| Polygon triangulation | $C_{n-2}$ | Ways to triangulate n-gon |

### 9.2 Mathematics

- Ballot problems
- Random walks
- Lattice paths
- Matrix chain multiplication orderings

## 10. Real-World Software Engineering Applications

### 10.1 Industry Use Cases

1. **Compiler Design**
   - Expression tree enumeration
   - Parser state counting

2. **Database Query Optimization**
   - Join order enumeration ($C_n$ orderings for n+1 tables)
   - Query plan counting

3. **Network Routing**
   - Non-crossing connection counting
   - VLSI layout problems

4. **Game AI**
   - Decision tree enumeration
   - Game state counting

### 10.2 Production Example: Expression Tree Analyzer

```python
from typing import List, Tuple, Optional
from dataclasses import dataclass
from enum import Enum


class NodeType(Enum):
    OPERAND = "operand"
    OPERATOR = "operator"


@dataclass
class ExpressionNode:
    """Node in expression tree."""
    value: str
    node_type: NodeType
    left: Optional['ExpressionNode'] = None
    right: Optional['ExpressionNode'] = None


class ExpressionTreeAnalyzer:
    """
    Analyze and enumerate expression tree structures.
    
    Uses Catalan numbers for counting.
    """
    
    def __init__(self):
        self._catalan_cache = [1]
    
    def _catalan(self, n: int) -> int:
        """Get nth Catalan number."""
        while len(self._catalan_cache) <= n:
            k = len(self._catalan_cache)
            c = 0
            for i in range(k):
                c += self._catalan_cache[i] * self._catalan_cache[k - 1 - i]
            self._catalan_cache.append(c)
        return self._catalan_cache[n]
    
    def count_tree_shapes(self, n_operands: int) -> int:
        """
        Count distinct expression tree shapes for n operands.
        
        >>> analyzer = ExpressionTreeAnalyzer()
        >>> analyzer.count_tree_shapes(4)
        5
        """
        if n_operands <= 0:
            return 0
        return self._catalan(n_operands - 1)
    
    def count_parenthesizations(self, n_operators: int) -> int:
        """
        Count ways to fully parenthesize expression.
        
        >>> analyzer = ExpressionTreeAnalyzer()
        >>> analyzer.count_parenthesizations(3)  # a op b op c op d
        5
        """
        return self._catalan(n_operators)
    
    def generate_all_trees(
        self, 
        operands: List[str], 
        operators: List[str]
    ) -> List[ExpressionNode]:
        """
        Generate all expression trees for given operands and operators.
        
        >>> analyzer = ExpressionTreeAnalyzer()
        >>> trees = analyzer.generate_all_trees(['a', 'b', 'c'], ['+', '*'])
        >>> len(trees)  # C(2) = 2
        2
        """
        if len(operands) != len(operators) + 1:
            raise ValueError("Need exactly n-1 operators for n operands")
        
        if len(operands) == 1:
            return [ExpressionNode(operands[0], NodeType.OPERAND)]
        
        trees = []
        
        def build_trees(
            ops: List[str],
            op_idx: int,
            op_remaining: List[str]
        ) -> List[ExpressionNode]:
            """Build all possible trees."""
            if len(ops) == 1:
                return [ExpressionNode(ops[0], NodeType.OPERAND)]
            
            result = []
            
            # Try each split point
            for i in range(1, len(ops)):
                left_ops = ops[:i]
                right_ops = ops[i:]
                
                # Get operator to use at root
                if len(op_remaining) == 0:
                    continue
                
                # Generate all combinations
                for op in op_remaining:
                    remaining = op_remaining.copy()
                    remaining.remove(op)
                    
                    # Distribute remaining operators
                    for left_count in range(len(left_ops)):
                        if len(left_ops) - 1 == left_count:
                            left_remaining = remaining[:left_count]
                            right_remaining = remaining[left_count:]
                            
                            for left_tree in build_trees(left_ops, 0, left_remaining):
                                for right_tree in build_trees(right_ops, 0, right_remaining):
                                    root = ExpressionNode(
                                        op, 
                                        NodeType.OPERATOR,
                                        left_tree,
                                        right_tree
                                    )
                                    result.append(root)
            
            return result
        
        # Simpler approach: just enumerate tree structures
        return self._enumerate_trees(operands, operators)
    
    def _enumerate_trees(
        self, 
        operands: List[str], 
        operators: List[str]
    ) -> List[ExpressionNode]:
        """
        Enumerate all expression trees using recursive splitting.
        """
        n = len(operands)
        
        if n == 1:
            return [ExpressionNode(operands[0], NodeType.OPERAND)]
        
        trees = []
        
        # Split at each position
        for i in range(1, n):
            left_operands = operands[:i]
            right_operands = operands[i:]
            
            left_trees = self._enumerate_trees(left_operands, operators[:i-1])
            right_trees = self._enumerate_trees(right_operands, operators[i:])
            
            # Combine with operator at split point
            op = operators[i - 1]
            
            for left in left_trees:
                for right in right_trees:
                    trees.append(ExpressionNode(
                        op,
                        NodeType.OPERATOR,
                        left,
                        right
                    ))
        
        return trees
    
    def tree_to_string(self, node: ExpressionNode) -> str:
        """
        Convert expression tree to fully parenthesized string.
        
        >>> analyzer = ExpressionTreeAnalyzer()
        >>> node = ExpressionNode('+', NodeType.OPERATOR,
        ...     ExpressionNode('a', NodeType.OPERAND),
        ...     ExpressionNode('b', NodeType.OPERAND))
        >>> analyzer.tree_to_string(node)
        '(a + b)'
        """
        if node.node_type == NodeType.OPERAND:
            return node.value
        
        left_str = self.tree_to_string(node.left)
        right_str = self.tree_to_string(node.right)
        
        return f"({left_str} {node.value} {right_str})"
    
    def matrix_chain_orderings(self, n_matrices: int) -> int:
        """
        Count parenthesizations for matrix chain multiplication.
        
        >>> analyzer = ExpressionTreeAnalyzer()
        >>> analyzer.matrix_chain_orderings(4)
        5
        """
        return self._catalan(n_matrices - 1)
    
    def query_join_orderings(self, n_tables: int) -> int:
        """
        Count join orderings for n tables in SQL query.
        
        Important for query optimization.
        
        >>> analyzer = ExpressionTreeAnalyzer()
        >>> analyzer.query_join_orderings(5)
        14
        """
        return self._catalan(n_tables - 1)


class SQLQueryOptimizer:
    """
    Demonstrate Catalan numbers in query optimization.
    """
    
    def __init__(self):
        self.analyzer = ExpressionTreeAnalyzer()
    
    def estimate_plan_space(
        self, 
        query: str, 
        n_tables: int
    ) -> Tuple[int, str]:
        """
        Estimate search space for query optimization.
        
        >>> optimizer = SQLQueryOptimizer()
        >>> count, message = optimizer.estimate_plan_space("SELECT...", 5)
        >>> count
        14
        """
        n_orderings = self.analyzer.query_join_orderings(n_tables)
        
        # With different join methods (nested loop, hash, merge)
        join_methods = 3
        total_plans = n_orderings * (join_methods ** (n_tables - 1))
        
        message = (
            f"Query with {n_tables} tables:\n"
            f"  - Join orderings: {n_orderings} (Catalan C_{n_tables-1})\n"
            f"  - With join methods: ~{total_plans:,} plans\n"
            f"  - Optimization complexity: {'exhaustive OK' if n_tables <= 10 else 'heuristic needed'}"
        )
        
        return n_orderings, message


# Example usage
analyzer = ExpressionTreeAnalyzer()

print("Expression Tree Analysis")
print("=" * 40)

# Count tree shapes
for n in range(1, 8):
    shapes = analyzer.count_tree_shapes(n)
    print(f"C({n}) = {shapes} tree shapes for {n} operands")

print()

# Generate all trees for a + b * c
operands = ['a', 'b', 'c']
operators = ['+', '*']
trees = analyzer.generate_all_trees(operands, operators)

print(f"All parenthesizations of a + b * c ({len(trees)} trees):")
for i, tree in enumerate(trees, 1):
    print(f"  {i}. {analyzer.tree_to_string(tree)}")

print()

# Query optimization
optimizer = SQLQueryOptimizer()
_, message = optimizer.estimate_plan_space("SELECT * FROM A,B,C,D,E", 5)
print(message)
```

## 11. Related Sequences

| Sequence | Formula | Relation |
|----------|---------|----------|
| Motzkin | $M_n$ | Paths with flat steps |
| Schröder | $S_n$ | Paths with diagonal steps |
| Bell | $B_n$ | All partitions |
| Fibonacci | $F_n$ | No direct relation |

## 12. Edge Cases

| Input | Output | Notes |
|-------|--------|-------|
| n < 0 | 0 | Invalid |
| n = 0 | 1 | Empty structure |
| n = 1 | 1 | Single element |
| Large n | Use mod | Overflow risk |

## 13. References

- [Wikipedia: Catalan Numbers](https://en.wikipedia.org/wiki/Catalan_number)
- [OEIS A000108](https://oeis.org/A000108)
- Stanley, R.P. "Catalan Numbers" (Cambridge, 2015)
