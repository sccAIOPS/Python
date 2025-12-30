# Binomial Coefficients

## Overview
- **Category**: Combinatorics
- **Complexity**: Time: O(n) | Space: O(1) or O(n²) for Pascal's triangle
- **Type**: Counting algorithm
- **Source File**: [maths/binomial_coefficient.py](../../../maths/binomial_coefficient.py)

## 1. Mathematical Foundation

### 1.1 Definition

The binomial coefficient $\binom{n}{k}$ (read "n choose k") counts the number of ways to choose $k$ elements from a set of $n$ elements:

$$
\binom{n}{k} = \frac{n!}{k!(n-k)!}
$$

### 1.2 Combinatorial Interpretation

- Number of k-element subsets of an n-element set
- Coefficients in $(a + b)^n$ expansion
- Paths in a grid from (0,0) to (k, n-k)

### 1.3 Key Properties

**Symmetry:**
$$
\binom{n}{k} = \binom{n}{n-k}
$$

**Pascal's Identity:**
$$
\binom{n}{k} = \binom{n-1}{k-1} + \binom{n-1}{k}
$$

**Sum of Row:**
$$
\sum_{k=0}^{n} \binom{n}{k} = 2^n
$$

**Hockey Stick Identity:**
$$
\sum_{i=r}^{n} \binom{i}{r} = \binom{n+1}{r+1}
$$

### 1.4 Binomial Theorem

$$
(a + b)^n = \sum_{k=0}^{n} \binom{n}{k} a^{n-k} b^k
$$

### 1.5 Bounds

$$
\left(\frac{n}{k}\right)^k \leq \binom{n}{k} \leq \frac{n^k}{k!} \leq \left(\frac{en}{k}\right)^k
$$

## 2. Computing Methods

### 2.1 Direct Formula (Factorial)

$$
\binom{n}{k} = \frac{n!}{k!(n-k)!}
$$

**Problem**: Factorial overflow for large n.

### 2.2 Multiplicative Formula

$$
\binom{n}{k} = \frac{n \cdot (n-1) \cdot \ldots \cdot (n-k+1)}{k!} = \prod_{i=1}^{k} \frac{n-k+i}{i}
$$

**Advantage**: Avoids computing full factorials.

### 2.3 Pascal's Triangle (DP)

Build using recurrence: $\binom{n}{k} = \binom{n-1}{k-1} + \binom{n-1}{k}$

### 2.4 Lucas' Theorem (Modular)

For prime $p$:
$$
\binom{n}{k} \equiv \prod_{i} \binom{n_i}{k_i} \pmod{p}
$$

where $n = \sum n_i p^i$ and $k = \sum k_i p^i$ are base-$p$ representations.

## 3. Pseudocode

```
ALGORITHM BinomialCoefficient-Multiplicative(n, k)
    INPUT: Non-negative integers n and k with k ≤ n
    OUTPUT: C(n, k)
    
    if k > n - k then
        k ← n - k  // Use symmetry for efficiency
    
    result ← 1
    for i ← 1 to k do
        result ← result × (n - k + i)
        result ← result / i
    
    return result

ALGORITHM PascalsTriangle(n)
    INPUT: Non-negative integer n
    OUTPUT: 2D array C where C[i][j] = C(i, j)
    
    // Initialize
    for i ← 0 to n do
        C[i][0] ← 1
        C[i][i] ← 1
    
    // Fill using recurrence
    for i ← 2 to n do
        for j ← 1 to i - 1 do
            C[i][j] ← C[i-1][j-1] + C[i-1][j]
    
    return C

ALGORITHM BinomialMod-Lucas(n, k, p)
    INPUT: Integers n, k and prime p
    OUTPUT: C(n, k) mod p
    
    result ← 1
    while n > 0 OR k > 0 do
        n_i ← n mod p
        k_i ← k mod p
        
        if k_i > n_i then
            return 0
        
        result ← result × C(n_i, k_i) mod p
        n ← n / p
        k ← k / p
    
    return result
```

## 4. Step-by-Step Example

### Example: Compute $\binom{10}{4}$

**Multiplicative formula**:
$$
\binom{10}{4} = \frac{10 \times 9 \times 8 \times 7}{4 \times 3 \times 2 \times 1} = \frac{5040}{24} = 210
$$

**Pascal's approach**:
```
         1
        1 1
       1 2 1
      1 3 3 1
     1 4 6 4 1
    1 5 10 10 5 1
   1 6 15 20 15 6 1
  1 7 21 35 35 21 7 1
 1 8 28 56 70 56 28 8 1
1 9 36 84 126 126 84 36 9 1
1 10 45 120 210 ...
```

$\binom{10}{4} = 210$ ✓

## 5. Complexity Analysis

| Method | Time | Space | Use Case |
|--------|------|-------|----------|
| Factorial | O(n) | O(1) | Small n only (overflow) |
| Multiplicative | O(k) | O(1) | Single query |
| Pascal (full) | O(n²) | O(n²) | Multiple queries |
| Pascal (1D) | O(nk) | O(k) | Single row |
| Lucas | O(log_p n × p) | O(p) | Modular, large n |

## 6. Visual Representation

### 6.1 Pascal's Triangle

```
Row 0:                  1
Row 1:                1   1
Row 2:              1   2   1
Row 3:            1   3   3   1
Row 4:          1   4   6   4   1
Row 5:        1   5  10  10   5   1
Row 6:      1   6  15  20  15   6   1
Row 7:    1   7  21  35  35  21   7   1

Each number = sum of two above
```

### 6.2 Grid Path Interpretation

```
From (0,0) to (3,2): C(5,2) = 10 paths

  0   1   2   3
  ●───●───●───●  0
  │   │   │   │
  ●───●───●───●  1
      │   │   │
      ●───●───●  2

Path = sequence of R(right) and D(down)
C(5,2) counts arrangements of RRR DD
```

## 7. Implementation

```python
from typing import List
from functools import lru_cache
import math


def binomial_multiplicative(n: int, k: int) -> int:
    """
    Compute C(n, k) using multiplicative formula.
    
    Avoids factorial overflow by computing iteratively.
    
    >>> binomial_multiplicative(10, 4)
    210
    >>> binomial_multiplicative(20, 10)
    184756
    >>> binomial_multiplicative(5, 0)
    1
    >>> binomial_multiplicative(5, 5)
    1
    """
    if k < 0 or k > n:
        return 0
    if k == 0 or k == n:
        return 1
    
    # Use symmetry
    if k > n - k:
        k = n - k
    
    result = 1
    for i in range(k):
        result = result * (n - i) // (i + 1)
    
    return result


def binomial_factorial(n: int, k: int) -> int:
    """
    Compute C(n, k) using factorial formula.
    
    >>> binomial_factorial(10, 4)
    210
    """
    if k < 0 or k > n:
        return 0
    return math.factorial(n) // (math.factorial(k) * math.factorial(n - k))


def pascals_triangle(n: int) -> List[List[int]]:
    """
    Generate Pascal's triangle up to row n.
    
    >>> pascals_triangle(5)
    [[1], [1, 1], [1, 2, 1], [1, 3, 3, 1], [1, 4, 6, 4, 1], [1, 5, 10, 10, 5, 1]]
    """
    triangle = [[1]]
    
    for i in range(1, n + 1):
        row = [1]
        for j in range(1, i):
            row.append(triangle[i-1][j-1] + triangle[i-1][j])
        row.append(1)
        triangle.append(row)
    
    return triangle


def binomial_dp(n: int, k: int) -> int:
    """
    Compute C(n, k) using dynamic programming.
    
    Space-optimized: O(k) instead of O(n²).
    
    >>> binomial_dp(10, 4)
    210
    """
    if k < 0 or k > n:
        return 0
    if k > n - k:
        k = n - k
    
    # dp[j] will store C(i, j) for current row i
    dp = [0] * (k + 1)
    dp[0] = 1
    
    for i in range(1, n + 1):
        # Update from right to left to avoid overwriting
        for j in range(min(i, k), 0, -1):
            dp[j] += dp[j - 1]
    
    return dp[k]


def binomial_mod(n: int, k: int, mod: int) -> int:
    """
    Compute C(n, k) mod m using modular arithmetic.
    
    >>> binomial_mod(10, 4, 1000000007)
    210
    >>> binomial_mod(1000, 500, 1000000007)
    159835829
    """
    if k < 0 or k > n:
        return 0
    if k > n - k:
        k = n - k
    
    # Compute factorials and inverse factorials
    numerator = 1
    denominator = 1
    
    for i in range(k):
        numerator = (numerator * (n - i)) % mod
        denominator = (denominator * (i + 1)) % mod
    
    # Modular inverse using Fermat's little theorem
    return (numerator * pow(denominator, mod - 2, mod)) % mod


def lucas_theorem(n: int, k: int, p: int) -> int:
    """
    Compute C(n, k) mod p using Lucas' theorem.
    
    Works for very large n, k when p is small prime.
    
    >>> lucas_theorem(1000, 500, 13)
    5
    >>> lucas_theorem(10**18, 10**9, 1000000007)
    0
    """
    if k > n:
        return 0
    
    # Precompute small binomial coefficients mod p
    @lru_cache(maxsize=None)
    def small_binomial(n: int, k: int) -> int:
        if k > n or k < 0:
            return 0
        if k == 0 or k == n:
            return 1
        return (small_binomial(n-1, k-1) + small_binomial(n-1, k)) % p
    
    result = 1
    while n > 0 or k > 0:
        n_i = n % p
        k_i = k % p
        
        if k_i > n_i:
            return 0
        
        result = (result * small_binomial(n_i, k_i)) % p
        n //= p
        k //= p
    
    return result


@lru_cache(maxsize=None)
def binomial_recursive(n: int, k: int) -> int:
    """
    Recursive computation with memoization.
    
    >>> binomial_recursive(10, 4)
    210
    """
    if k < 0 or k > n:
        return 0
    if k == 0 or k == n:
        return 1
    return binomial_recursive(n - 1, k - 1) + binomial_recursive(n - 1, k)


class BinomialPrecomputed:
    """
    Class for precomputed binomial coefficients.
    
    Useful when many queries are needed.
    """
    
    def __init__(self, max_n: int, mod: int = 10**9 + 7):
        self.mod = mod
        self.max_n = max_n
        
        # Precompute factorials and inverse factorials
        self.fact = [1] * (max_n + 1)
        self.inv_fact = [1] * (max_n + 1)
        
        for i in range(1, max_n + 1):
            self.fact[i] = (self.fact[i-1] * i) % mod
        
        self.inv_fact[max_n] = pow(self.fact[max_n], mod - 2, mod)
        for i in range(max_n - 1, -1, -1):
            self.inv_fact[i] = (self.inv_fact[i+1] * (i+1)) % mod
    
    def C(self, n: int, k: int) -> int:
        """
        Query C(n, k) in O(1) time.
        
        >>> bc = BinomialPrecomputed(1000)
        >>> bc.C(10, 4)
        210
        """
        if k < 0 or k > n or n > self.max_n:
            return 0
        return (self.fact[n] * self.inv_fact[k] % self.mod 
                * self.inv_fact[n-k] % self.mod)
    
    def P(self, n: int, k: int) -> int:
        """
        Permutation P(n, k) = n!/(n-k)!
        """
        if k < 0 or k > n or n > self.max_n:
            return 0
        return self.fact[n] * self.inv_fact[n-k] % self.mod
```

## 8. Applications

### 8.1 Counting Problems

- Subsets: $\binom{n}{k}$ ways to choose k from n
- Multisets: $\binom{n+k-1}{k}$ ways to choose k from n with repetition
- Lattice paths: $\binom{m+n}{n}$ paths from (0,0) to (m,n)

### 8.2 Probability

Binomial distribution:
$$
P(X = k) = \binom{n}{k} p^k (1-p)^{n-k}
$$

### 8.3 Algebra

- Polynomial expansion
- Generating functions
- Identities

## 9. Real-World Software Engineering Applications

### 9.1 Industry Use Cases

1. **Statistics/ML**
   - Binomial distribution calculations
   - Feature selection
   - Cross-validation splits

2. **Cryptography**
   - Secret sharing schemes
   - Error-correcting codes

3. **Competitive Programming**
   - Combinatorial counting
   - Dynamic programming states

4. **Game Development**
   - Probability calculations
   - Procedural generation

### 9.2 Production Example: Combinatorial Calculator

```python
from typing import List, Tuple


class CombinatorialCalculator:
    """
    Comprehensive combinatorial calculations.
    """
    
    def __init__(self, mod: int = 10**9 + 7):
        self.mod = mod
        self._cache_size = 0
        self._fact = [1]
        self._inv_fact = [1]
    
    def _extend_cache(self, n: int) -> None:
        """Extend factorial cache to size n."""
        if n <= self._cache_size:
            return
        
        # Extend factorials
        for i in range(self._cache_size + 1, n + 1):
            self._fact.append((self._fact[-1] * i) % self.mod)
        
        # Extend inverse factorials
        self._inv_fact = [1] * (n + 1)
        self._inv_fact[n] = pow(self._fact[n], self.mod - 2, self.mod)
        for i in range(n - 1, -1, -1):
            self._inv_fact[i] = (self._inv_fact[i + 1] * (i + 1)) % self.mod
        
        self._cache_size = n
    
    def combinations(self, n: int, k: int) -> int:
        """
        C(n, k) = ways to choose k items from n.
        
        >>> calc = CombinatorialCalculator()
        >>> calc.combinations(52, 5)  # Poker hands
        2598960
        """
        if k < 0 or k > n:
            return 0
        self._extend_cache(n)
        return (self._fact[n] * self._inv_fact[k] % self.mod 
                * self._inv_fact[n - k] % self.mod)
    
    def permutations(self, n: int, k: int) -> int:
        """
        P(n, k) = ordered arrangements of k items from n.
        
        >>> calc = CombinatorialCalculator()
        >>> calc.permutations(5, 3)
        60
        """
        if k < 0 or k > n:
            return 0
        self._extend_cache(n)
        return self._fact[n] * self._inv_fact[n - k] % self.mod
    
    def combinations_with_repetition(self, n: int, k: int) -> int:
        """
        Ways to choose k items from n with repetition allowed.
        
        Formula: C(n + k - 1, k)
        
        >>> calc = CombinatorialCalculator()
        >>> calc.combinations_with_repetition(3, 2)  # aa, ab, ac, bb, bc, cc
        6
        """
        return self.combinations(n + k - 1, k)
    
    def multinomial(self, n: int, groups: List[int]) -> int:
        """
        Multinomial coefficient: n! / (k1! * k2! * ... * km!)
        
        Ways to divide n items into groups of sizes k1, k2, ..., km.
        
        >>> calc = CombinatorialCalculator()
        >>> calc.multinomial(10, [3, 3, 4])
        4200
        """
        if sum(groups) != n:
            raise ValueError("Group sizes must sum to n")
        
        self._extend_cache(n)
        result = self._fact[n]
        for k in groups:
            result = result * self._inv_fact[k] % self.mod
        return result
    
    def derangements(self, n: int) -> int:
        """
        Number of derangements (permutations with no fixed points).
        
        D(n) = n! * Σ((-1)^k / k!) for k=0 to n
        
        >>> calc = CombinatorialCalculator()
        >>> calc.derangements(4)
        9
        """
        if n == 0:
            return 1
        if n == 1:
            return 0
        
        # Use recurrence: D(n) = (n-1) * (D(n-1) + D(n-2))
        d_prev2, d_prev1 = 1, 0
        for i in range(2, n + 1):
            d_curr = ((i - 1) * (d_prev1 + d_prev2)) % self.mod
            d_prev2, d_prev1 = d_prev1, d_curr
        
        return d_prev1
    
    def catalan(self, n: int) -> int:
        """
        nth Catalan number.
        
        C(n) = C(2n, n) / (n + 1)
        
        >>> calc = CombinatorialCalculator()
        >>> calc.catalan(5)
        42
        """
        self._extend_cache(2 * n)
        return (self.combinations(2 * n, n) 
                * pow(n + 1, self.mod - 2, self.mod) % self.mod)
    
    def stirling_second(self, n: int, k: int) -> int:
        """
        Stirling number of the second kind S(n, k).
        
        Number of ways to partition n elements into k non-empty subsets.
        
        >>> calc = CombinatorialCalculator()
        >>> calc.stirling_second(4, 2)
        7
        """
        if k == 0:
            return 1 if n == 0 else 0
        if k > n:
            return 0
        
        # S(n, k) = (1/k!) * Σ(-1)^(k-j) * C(k, j) * j^n
        result = 0
        for j in range(k + 1):
            term = self.combinations(k, j) * pow(j, n, self.mod) % self.mod
            if (k - j) % 2 == 0:
                result = (result + term) % self.mod
            else:
                result = (result - term + self.mod) % self.mod
        
        return result * self._inv_fact[k] % self.mod


# Example: Poker probability calculations
calc = CombinatorialCalculator(mod=10**18 + 9)  # Large mod for exact calc

# Total 5-card hands
total_hands = calc.combinations(52, 5)
print(f"Total 5-card hands: {total_hands}")

# Royal flush: 4 suits × 1 combination each
royal_flushes = 4

# Straight flush (excluding royal): 4 suits × 9 ranks (A-5 through 9-K)
straight_flushes = 4 * 9

# Four of a kind: 13 ranks × C(4,4) × 48 kickers
four_of_kind = 13 * 1 * 48

# Full house: 13 ranks for triple × C(4,3) × 12 ranks for pair × C(4,2)
full_house = 13 * 4 * 12 * 6

print(f"\nPoker hand probabilities:")
print(f"Royal Flush: {royal_flushes}/{total_hands} = {royal_flushes/total_hands:.8f}")
print(f"Straight Flush: {straight_flushes}/{total_hands} = {straight_flushes/total_hands:.8f}")
print(f"Four of a Kind: {four_of_kind}/{total_hands} = {four_of_kind/total_hands:.6f}")
print(f"Full House: {full_house}/{total_hands} = {full_house/total_hands:.6f}")
```

## 10. Identities Reference

| Identity | Formula |
|----------|---------|
| Symmetry | $\binom{n}{k} = \binom{n}{n-k}$ |
| Pascal | $\binom{n}{k} = \binom{n-1}{k-1} + \binom{n-1}{k}$ |
| Row Sum | $\sum_k \binom{n}{k} = 2^n$ |
| Alternating | $\sum_k (-1)^k \binom{n}{k} = 0$ |
| Vandermonde | $\sum_k \binom{m}{k}\binom{n}{r-k} = \binom{m+n}{r}$ |
| Hockey Stick | $\sum_{i=r}^n \binom{i}{r} = \binom{n+1}{r+1}$ |

## 11. Edge Cases

| Input | Output | Notes |
|-------|--------|-------|
| k < 0 | 0 | Invalid |
| k > n | 0 | Not enough elements |
| k = 0 | 1 | Empty set |
| k = n | 1 | Full set |
| n = 0 | 1 if k=0, else 0 | Empty set |

## 12. References

- [Wikipedia: Binomial Coefficient](https://en.wikipedia.org/wiki/Binomial_coefficient)
- Graham, Knuth, Patashnik. "Concrete Mathematics"
- [OEIS A007318](https://oeis.org/A007318) (Pascal's Triangle)
