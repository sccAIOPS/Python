# Matrix Exponentiation

## Overview
- **Category**: Linear Algebra / Dynamic Programming
- **Complexity**: Time: O(k³ log n) | Space: O(k²)
- **Type**: Divide and conquer
- **Source File**: [maths/matrix_exponentiation.py](../../../maths/matrix_exponentiation.py)

## 1. Mathematical Foundation

### 1.1 Problem Definition

Compute $A^n$ for a $k \times k$ matrix $A$ efficiently.

Direct computation requires $O(n)$ matrix multiplications, each $O(k^3)$:
- Total: $O(nk^3)$ — too slow for large $n$

### 1.2 Binary Exponentiation for Matrices

Same principle as scalar exponentiation:

$$
A^n = \begin{cases}
I & \text{if } n = 0 \\
(A^{n/2})^2 & \text{if } n \text{ is even} \\
A \cdot (A^{(n-1)/2})^2 & \text{if } n \text{ is odd}
\end{cases}
$$

### 1.3 Linear Recurrences

Any linear recurrence can be expressed as matrix multiplication:

$$
\begin{pmatrix} f_n \\ f_{n-1} \\ \vdots \\ f_{n-k+1} \end{pmatrix}
= M \cdot
\begin{pmatrix} f_{n-1} \\ f_{n-2} \\ \vdots \\ f_{n-k} \end{pmatrix}
$$

For $f_n = c_1 f_{n-1} + c_2 f_{n-2} + \cdots + c_k f_{n-k}$:

$$
M = \begin{pmatrix}
c_1 & c_2 & c_3 & \cdots & c_k \\
1 & 0 & 0 & \cdots & 0 \\
0 & 1 & 0 & \cdots & 0 \\
\vdots & & \ddots & & \vdots \\
0 & 0 & \cdots & 1 & 0
\end{pmatrix}
$$

### 1.4 Fibonacci Example

$F_n = F_{n-1} + F_{n-2}$

$$
\begin{pmatrix} F_n \\ F_{n-1} \end{pmatrix}
= \begin{pmatrix} 1 & 1 \\ 1 & 0 \end{pmatrix}
\begin{pmatrix} F_{n-1} \\ F_{n-2} \end{pmatrix}
$$

Therefore:
$$
\begin{pmatrix} F_n \\ F_{n-1} \end{pmatrix}
= \begin{pmatrix} 1 & 1 \\ 1 & 0 \end{pmatrix}^{n-1}
\begin{pmatrix} F_1 \\ F_0 \end{pmatrix}
= \begin{pmatrix} 1 & 1 \\ 1 & 0 \end{pmatrix}^{n-1}
\begin{pmatrix} 1 \\ 0 \end{pmatrix}
$$

## 2. Algorithm Description

### 2.1 Intuition

1. Apply binary exponentiation to matrices
2. Number of multiplications: $O(\log n)$
3. Each multiplication: $O(k^3)$
4. Total: $O(k^3 \log n)$

### 2.2 Identity Matrix

The identity matrix $I$ serves as the multiplicative identity:
$$
A \cdot I = I \cdot A = A
$$

## 3. Pseudocode

```
ALGORITHM MatrixExponentiation(A, n)
    INPUT: k×k matrix A, non-negative integer n
    OUTPUT: A^n
    
    k ← number of rows in A
    result ← IdentityMatrix(k)
    base ← A
    
    while n > 0 do
        if n is odd then
            result ← MatrixMultiply(result, base)
        base ← MatrixMultiply(base, base)
        n ← n / 2
    
    return result

ALGORITHM MatrixMultiply(A, B)
    INPUT: Two k×k matrices A and B
    OUTPUT: Product A × B
    
    k ← number of rows
    C ← new k×k matrix of zeros
    
    for i ← 0 to k-1 do
        for j ← 0 to k-1 do
            for p ← 0 to k-1 do
                C[i][j] ← C[i][j] + A[i][p] × B[p][j]
    
    return C
```

## 4. Step-by-Step Example

### Example: Compute $F_{10}$ using Matrix Exponentiation

$M = \begin{pmatrix} 1 & 1 \\ 1 & 0 \end{pmatrix}$, compute $M^9$

**Binary**: $9 = 1001_2$

| Step | n | Action | Result |
|------|---|--------|--------|
| 0 | 9 (odd) | result = I × M | M |
| 1 | 4 (even) | base = M² | $\begin{pmatrix} 2 & 1 \\ 1 & 1 \end{pmatrix}$ |
| 2 | 2 (even) | base = M⁴ | $\begin{pmatrix} 5 & 3 \\ 3 & 2 \end{pmatrix}$ |
| 3 | 1 (odd) | result = M × M⁸ | $M^9$ |

$M^9 = \begin{pmatrix} 55 & 34 \\ 34 & 21 \end{pmatrix}$

$F_{10} = M^9[0][0] = 55$ ✓

## 5. Complexity Analysis

### 5.1 Time Complexity

| Component | Operations |
|-----------|------------|
| Matrix multiplication | $O(k^3)$ |
| Number of multiplications | $O(\log n)$ |
| **Total** | $O(k^3 \log n)$ |

With Strassen's algorithm: $O(k^{2.807} \log n)$

### 5.2 Space Complexity

- Store matrices: $O(k^2)$
- Recursion depth: $O(\log n)$ or $O(1)$ iterative

### 5.3 Comparison

| Method | Time | Use Case |
|--------|------|----------|
| Matrix Exponentiation | $O(k^3 \log n)$ | Fixed recurrence |
| Naive iteration | $O(kn)$ | Small n |
| Closed form | $O(1)$ | When available |

## 6. Visual Representation

### 6.1 Binary Exponentiation Tree

```
            M^13
           /    \
        M^6      × M
        /  \
     M^3   (squared)
     / \
   M^1  × M
    |
    M   (squared twice more)

13 = 1101₂
Multiplications: 3 + 3 = 6 (instead of 12)
```

### 6.2 Matrix Chain

```
Fibonacci transition:

[F₁]   [1 1]¹   [1]     [F₂]   [1 1]²   [1]
[F₀] = [1 0]  × [0]  →  [F₁] = [1 0]  × [0] → ...

      ↓ After n-1 applications

[Fₙ  ]   [1 1]ⁿ⁻¹   [1]
[Fₙ₋₁] = [1 0]    × [0]
```

## 7. Implementation

```python
from typing import List
from copy import deepcopy


Matrix = List[List[int]]


def matrix_multiply(A: Matrix, B: Matrix, mod: int = None) -> Matrix:
    """
    Multiply two square matrices.
    
    >>> A = [[1, 2], [3, 4]]
    >>> B = [[5, 6], [7, 8]]
    >>> matrix_multiply(A, B)
    [[19, 22], [43, 50]]
    """
    n = len(A)
    C = [[0] * n for _ in range(n)]
    
    for i in range(n):
        for j in range(n):
            for k in range(n):
                C[i][j] += A[i][k] * B[k][j]
                if mod:
                    C[i][j] %= mod
    
    return C


def identity_matrix(n: int) -> Matrix:
    """Create n×n identity matrix."""
    return [[1 if i == j else 0 for j in range(n)] for i in range(n)]


def matrix_power(A: Matrix, n: int, mod: int = None) -> Matrix:
    """
    Compute A^n using binary exponentiation.
    
    >>> A = [[1, 1], [1, 0]]
    >>> matrix_power(A, 9)  # Fibonacci
    [[55, 34], [34, 21]]
    >>> matrix_power(A, 10)[0][0]  # F(11)
    89
    """
    if n == 0:
        return identity_matrix(len(A))
    
    k = len(A)
    result = identity_matrix(k)
    base = deepcopy(A)
    
    while n > 0:
        if n & 1:
            result = matrix_multiply(result, base, mod)
        base = matrix_multiply(base, base, mod)
        n >>= 1
    
    return result


def fibonacci(n: int, mod: int = 10**9 + 7) -> int:
    """
    Compute nth Fibonacci number using matrix exponentiation.
    
    >>> fibonacci(10)
    55
    >>> fibonacci(50)
    586268941
    >>> fibonacci(0)
    0
    >>> fibonacci(1)
    1
    """
    if n <= 1:
        return n
    
    M = [[1, 1], [1, 0]]
    result = matrix_power(M, n - 1, mod)
    return result[0][0]


def tribonacci(n: int, mod: int = 10**9 + 7) -> int:
    """
    Compute nth Tribonacci number.
    T(n) = T(n-1) + T(n-2) + T(n-3), T(0)=0, T(1)=0, T(2)=1
    
    >>> tribonacci(10)
    81
    >>> tribonacci(25)
    423559
    """
    if n < 2:
        return 0
    if n == 2:
        return 1
    
    M = [[1, 1, 1],
         [1, 0, 0],
         [0, 1, 0]]
    
    result = matrix_power(M, n - 2, mod)
    return result[0][0]


def linear_recurrence(
    coefficients: List[int],
    initial_values: List[int],
    n: int,
    mod: int = 10**9 + 7
) -> int:
    """
    Solve general linear recurrence using matrix exponentiation.
    
    f(n) = c[0]*f(n-1) + c[1]*f(n-2) + ... + c[k-1]*f(n-k)
    
    Args:
        coefficients: [c[0], c[1], ..., c[k-1]]
        initial_values: [f(0), f(1), ..., f(k-1)]
        n: Index to compute
        mod: Modulus for computation
    
    >>> linear_recurrence([1, 1], [0, 1], 10)  # Fibonacci
    55
    >>> linear_recurrence([1, 1, 1], [0, 0, 1], 10)  # Tribonacci
    81
    """
    k = len(coefficients)
    
    if n < k:
        return initial_values[n] % mod
    
    # Build transition matrix
    M = [[0] * k for _ in range(k)]
    for j in range(k):
        M[0][j] = coefficients[j] % mod
    for i in range(1, k):
        M[i][i - 1] = 1
    
    # Compute M^(n-k+1)
    result = matrix_power(M, n - k + 1, mod)
    
    # Multiply with initial state [f(k-1), f(k-2), ..., f(0)]
    state = list(reversed(initial_values))
    
    answer = 0
    for j in range(k):
        answer = (answer + result[0][j] * state[j]) % mod
    
    return answer


def count_paths_in_graph(adj: Matrix, n: int, mod: int = 10**9 + 7) -> Matrix:
    """
    Count paths of length n in a graph.
    
    If A is adjacency matrix, A^n[i][j] = number of paths from i to j of length n.
    
    >>> adj = [[0, 1, 0], [0, 0, 1], [1, 0, 0]]  # Cycle graph
    >>> count_paths_in_graph(adj, 3)[0][0]  # Paths from 0 to 0 of length 3
    1
    """
    return matrix_power(adj, n, mod)


def solve_system_of_recurrences(
    A: Matrix,
    initial: List[int],
    n: int,
    mod: int = 10**9 + 7
) -> List[int]:
    """
    Solve system of linear recurrences.
    
    x(n) = A * x(n-1)
    
    >>> A = [[1, 1], [1, 0]]
    >>> solve_system_of_recurrences(A, [1, 0], 10)
    [89, 55]
    """
    if n == 0:
        return initial
    
    result_matrix = matrix_power(A, n, mod)
    
    k = len(initial)
    result = [0] * k
    
    for i in range(k):
        for j in range(k):
            result[i] = (result[i] + result_matrix[i][j] * initial[j]) % mod
    
    return result
```

## 8. Applications

### 8.1 Fibonacci and Related Sequences

All linear recurrences can be computed in $O(k^3 \log n)$.

### 8.2 Graph Path Counting

$A^n[i][j]$ = number of paths from $i$ to $j$ of length exactly $n$.

### 8.3 Markov Chains

State distribution after $n$ steps: $\pi_n = M^n \pi_0$

### 8.4 Polynomial Operations

Certain polynomial computations involve matrix exponentiation.

## 9. Real-World Software Engineering Applications

### 9.1 Industry Use Cases

1. **Competitive Programming**
   - Fast Fibonacci computation
   - Linear recurrence problems
   - Graph path counting

2. **Cryptography**
   - Linear feedback shift registers
   - Stream cipher analysis

3. **Quantitative Finance**
   - Markov chain models
   - Option pricing models

4. **Network Analysis**
   - Reachability in k steps
   - Random walk analysis

### 9.2 Production Example: Markov Chain Analyzer

```python
from typing import List, Tuple
import numpy as np


class MarkovChainAnalyzer:
    """
    Analyze Markov chains using matrix exponentiation.
    """
    
    def __init__(self, transition_matrix: Matrix):
        """
        Initialize with transition probability matrix.
        
        Args:
            transition_matrix: P[i][j] = probability of going from state i to j
        """
        self.P = transition_matrix
        self.n_states = len(transition_matrix)
        self._validate()
    
    def _validate(self) -> None:
        """Validate transition matrix (rows sum to 1)."""
        for i, row in enumerate(self.P):
            row_sum = sum(row)
            if abs(row_sum - 1.0) > 1e-6:
                raise ValueError(f"Row {i} doesn't sum to 1: {row_sum}")
    
    def _matrix_multiply_float(self, A: Matrix, B: Matrix) -> Matrix:
        """Multiply matrices with floating point."""
        n = len(A)
        C = [[0.0] * n for _ in range(n)]
        for i in range(n):
            for j in range(n):
                for k in range(n):
                    C[i][j] += A[i][k] * B[k][j]
        return C
    
    def _matrix_power_float(self, A: Matrix, n: int) -> Matrix:
        """Matrix exponentiation for floats."""
        k = len(A)
        result = [[1.0 if i == j else 0.0 for j in range(k)] for i in range(k)]
        base = [row[:] for row in A]
        
        while n > 0:
            if n & 1:
                result = self._matrix_multiply_float(result, base)
            base = self._matrix_multiply_float(base, base)
            n >>= 1
        
        return result
    
    def state_after_n_steps(
        self,
        initial_distribution: List[float],
        n: int
    ) -> List[float]:
        """
        Compute state distribution after n steps.
        
        Args:
            initial_distribution: Probability of starting in each state
            n: Number of steps
        
        Returns:
            Probability distribution after n steps
        
        >>> mc = MarkovChainAnalyzer([[0.7, 0.3], [0.4, 0.6]])
        >>> dist = mc.state_after_n_steps([1.0, 0.0], 1)
        >>> abs(dist[0] - 0.7) < 1e-6
        True
        """
        P_n = self._matrix_power_float(self.P, n)
        
        result = [0.0] * self.n_states
        for j in range(self.n_states):
            for i in range(self.n_states):
                result[j] += initial_distribution[i] * P_n[i][j]
        
        return result
    
    def stationary_distribution(self, iterations: int = 1000) -> List[float]:
        """
        Compute stationary distribution using power iteration.
        
        For ergodic chains: π = lim_{n→∞} P^n[i]
        """
        # Start with uniform distribution
        dist = [1.0 / self.n_states] * self.n_states
        
        # Use matrix exponentiation to converge faster
        P_high = self._matrix_power_float(self.P, iterations)
        
        # Any row of P^n converges to stationary distribution
        return P_high[0]
    
    def expected_hitting_time(
        self,
        start_state: int,
        target_state: int,
        max_steps: int = 10000
    ) -> float:
        """
        Compute expected hitting time E[T_j | X_0 = i].
        
        Uses matrix computation approach.
        """
        # Remove target state and compute fundamental matrix
        n = self.n_states
        if start_state == target_state:
            return 0.0
        
        # Create Q matrix (transition matrix without target state)
        Q = []
        state_map = {}
        idx = 0
        for i in range(n):
            if i != target_state:
                state_map[idx] = i
                idx += 1
        
        m = n - 1  # Size of Q
        for i in range(m):
            row = []
            for j in range(m):
                orig_i = state_map[i]
                orig_j = state_map[j]
                row.append(self.P[orig_i][orig_j])
            Q.append(row)
        
        # Expected hitting time is sum of (I-Q)^{-1} row for start state
        # Using numpy for matrix inversion
        Q_np = np.array(Q)
        I = np.eye(m)
        N = np.linalg.inv(I - Q_np)  # Fundamental matrix
        
        # Find start state index in reduced system
        start_idx = None
        for idx, orig in state_map.items():
            if orig == start_state:
                start_idx = idx
                break
        
        if start_idx is None:
            return float('inf')
        
        # Expected hitting time = sum of row in N
        return N[start_idx].sum()
    
    def absorption_probability(
        self,
        absorbing_states: List[int],
        start_state: int
    ) -> dict:
        """
        Compute probability of being absorbed in each absorbing state.
        
        Returns dict mapping absorbing state to probability.
        """
        # Partition states into transient and absorbing
        transient = [i for i in range(self.n_states) if i not in absorbing_states]
        
        if not transient:
            # All states absorbing
            return {start_state: 1.0} if start_state in absorbing_states else {}
        
        # Build Q (transient → transient) and R (transient → absorbing)
        t = len(transient)
        a = len(absorbing_states)
        
        Q = [[0.0] * t for _ in range(t)]
        R = [[0.0] * a for _ in range(t)]
        
        trans_idx = {s: i for i, s in enumerate(transient)}
        abs_idx = {s: i for i, s in enumerate(absorbing_states)}
        
        for i, s in enumerate(transient):
            for j, s2 in enumerate(transient):
                Q[i][j] = self.P[s][s2]
            for j, s2 in enumerate(absorbing_states):
                R[i][j] = self.P[s][s2]
        
        # Absorption probabilities: B = (I-Q)^{-1} R
        Q_np = np.array(Q)
        R_np = np.array(R)
        I = np.eye(t)
        
        N = np.linalg.inv(I - Q_np)
        B = N @ R_np
        
        if start_state in absorbing_states:
            return {start_state: 1.0}
        
        start_idx = trans_idx.get(start_state)
        if start_idx is None:
            return {}
        
        return {absorbing_states[j]: B[start_idx][j] 
                for j in range(a)}


# Example: Weather model
weather = MarkovChainAnalyzer([
    [0.7, 0.2, 0.1],  # Sunny → Sunny, Cloudy, Rainy
    [0.3, 0.4, 0.3],  # Cloudy → ...
    [0.2, 0.3, 0.5],  # Rainy → ...
])

# Probability distribution after 10 days starting from sunny
dist = weather.state_after_n_steps([1.0, 0.0, 0.0], 10)
print(f"After 10 days (started sunny): {[f'{p:.3f}' for p in dist]}")

# Stationary distribution
stationary = weather.stationary_distribution()
print(f"Long-term distribution: {[f'{p:.3f}' for p in stationary]}")
```

### 9.3 Graph Algorithm Applications

```python
from typing import Set


class GraphPathCounter:
    """
    Count paths in graphs using matrix exponentiation.
    """
    
    def __init__(self, n_vertices: int, edges: List[Tuple[int, int]]):
        """
        Initialize graph from edge list.
        
        Args:
            n_vertices: Number of vertices
            edges: List of (from, to) edges
        """
        self.n = n_vertices
        self.adj = [[0] * n_vertices for _ in range(n_vertices)]
        
        for u, v in edges:
            self.adj[u][v] = 1
    
    def paths_of_length_k(self, k: int, mod: int = 10**9 + 7) -> Matrix:
        """
        Count paths of exactly length k between all pairs.
        
        Returns matrix where result[i][j] = number of paths from i to j.
        
        >>> g = GraphPathCounter(3, [(0, 1), (1, 2), (2, 0)])
        >>> g.paths_of_length_k(3)[0][0]  # Cycle back to start
        1
        """
        return matrix_power(self.adj, k, mod)
    
    def paths_up_to_length_k(
        self,
        k: int,
        mod: int = 10**9 + 7
    ) -> Matrix:
        """
        Count paths of length 1 to k between all pairs.
        
        Uses geometric series: I + A + A² + ... + A^k = (A^{k+1} - I)(A - I)^{-1}
        But we'll compute iteratively for simplicity.
        """
        n = self.n
        result = [[0] * n for _ in range(n)]
        current = identity_matrix(n)
        
        for _ in range(k):
            current = matrix_multiply(current, self.adj, mod)
            for i in range(n):
                for j in range(n):
                    result[i][j] = (result[i][j] + current[i][j]) % mod
        
        return result
    
    def reachable_in_k_steps(
        self,
        start: int,
        k: int
    ) -> Set[int]:
        """
        Find all vertices reachable from start in exactly k steps.
        """
        A_k = matrix_power(self.adj, k)
        return {j for j in range(self.n) if A_k[start][j] > 0}


# Example: Directed graph
edges = [(0, 1), (1, 2), (2, 3), (3, 0), (1, 3)]
graph = GraphPathCounter(4, edges)

# Count paths of length 5 from vertex 0 to vertex 2
paths = graph.paths_of_length_k(5)
print(f"Paths of length 5 from 0 to 2: {paths[0][2]}")

# Reachable vertices
reachable = graph.reachable_in_k_steps(0, 3)
print(f"Reachable from 0 in 3 steps: {reachable}")
```

## 10. Optimizations

### 10.1 Strassen's Algorithm

Reduce matrix multiplication from $O(k^3)$ to $O(k^{2.807})$.

### 10.2 Sparse Matrices

For sparse transition matrices, use sparse matrix multiplication.

### 10.3 Eigenvalue Decomposition

If $A = PDP^{-1}$, then $A^n = PD^nP^{-1}$ — diagonal matrices are trivial to exponentiate.

## 11. Edge Cases

| Input | Output | Notes |
|-------|--------|-------|
| n = 0 | Identity matrix | $A^0 = I$ |
| n = 1 | Original matrix | $A^1 = A$ |
| 1×1 matrix | Scalar power | Reduces to scalar exponentiation |
| Zero matrix | Zero matrix | $0^n = 0$ for $n > 0$ |

## 12. References

- [Wikipedia: Matrix Exponentiation](https://en.wikipedia.org/wiki/Matrix_exponential)
- Cormen et al. "Introduction to Algorithms" (Chapter 28)
- [CP Algorithms: Matrix Exponentiation](https://cp-algorithms.com/algebra/matrix-exp.html)
