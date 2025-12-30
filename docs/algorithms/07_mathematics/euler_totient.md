# Euler's Totient Function

## Overview
- **Category**: Number Theory
- **Complexity**: Time: O(√n) | Space: O(1)
- **Type**: Arithmetic function
- **Source File**: [maths/euler_totient.py](../../../maths/euler_totient.py)

## 1. Mathematical Foundation

### 1.1 Definition

Euler's totient function $\phi(n)$ counts the number of integers from 1 to $n$ that are coprime to $n$:

$$
\phi(n) = |\{k : 1 \leq k \leq n, \gcd(k, n) = 1\}|
$$

### 1.2 Basic Values

| n | φ(n) | Coprime elements |
|---|------|-----------------|
| 1 | 1 | {1} |
| 2 | 1 | {1} |
| 6 | 2 | {1, 5} |
| 8 | 4 | {1, 3, 5, 7} |
| 12 | 4 | {1, 5, 7, 11} |

### 1.3 Fundamental Properties

**Property 1: Prime p**
$$
\phi(p) = p - 1
$$

**Property 2: Prime power**
$$
\phi(p^k) = p^{k-1}(p - 1) = p^k - p^{k-1}
$$

**Property 3: Multiplicative (key property)**

For $\gcd(m, n) = 1$:
$$
\phi(mn) = \phi(m) \cdot \phi(n)
$$

### 1.4 Product Formula

For $n = p_1^{a_1} p_2^{a_2} \cdots p_k^{a_k}$:

$$
\phi(n) = n \prod_{p | n} \left(1 - \frac{1}{p}\right) = n \cdot \frac{p_1 - 1}{p_1} \cdot \frac{p_2 - 1}{p_2} \cdots \frac{p_k - 1}{p_k}
$$

### 1.5 Euler's Theorem

For $\gcd(a, n) = 1$:
$$
a^{\phi(n)} \equiv 1 \pmod{n}
$$

**Fermat's Little Theorem** is a special case when $n = p$ is prime:
$$
a^{p-1} \equiv 1 \pmod{p}
$$

### 1.6 Gauss's Sum

$$
\sum_{d | n} \phi(d) = n
$$

For example: $\phi(1) + \phi(2) + \phi(4) = 1 + 1 + 2 = 4$

## 2. Algorithm Description

### 2.1 Naive Approach

Count coprimes directly: $O(n \log n)$

### 2.2 Product Formula Approach

1. Find all prime factors of $n$
2. Apply the product formula
3. Time: $O(\sqrt{n})$

### 2.3 Sieve Approach

Compute $\phi(k)$ for all $k \leq n$ in $O(n \log \log n)$

## 3. Pseudocode

```
ALGORITHM EulerTotient(n)
    INPUT: Positive integer n
    OUTPUT: φ(n)
    
    result ← n
    
    // Find prime factors
    for p ← 2 while p² ≤ n do
        if n mod p = 0 then
            // Remove factor p
            while n mod p = 0 do
                n ← n / p
            // Apply formula: result = result × (1 - 1/p)
            result ← result - result / p
        p ← p + 1
    
    // If n > 1, then it's a prime factor
    if n > 1 then
        result ← result - result / n
    
    return result

ALGORITHM TotientSieve(n)
    INPUT: Positive integer n
    OUTPUT: Array phi[1..n] where phi[i] = φ(i)
    
    // Initialize phi[i] = i
    for i ← 1 to n do
        phi[i] ← i
    
    // Sieve
    for i ← 2 to n do
        if phi[i] = i then  // i is prime
            for j ← i to n step i do
                phi[j] ← phi[j] - phi[j] / i
    
    return phi
```

## 4. Step-by-Step Example

### Example: Compute φ(36)

$36 = 2^2 \times 3^2$

**Method 1: Product Formula**
$$
\phi(36) = 36 \times \left(1 - \frac{1}{2}\right) \times \left(1 - \frac{1}{3}\right) = 36 \times \frac{1}{2} \times \frac{2}{3} = 12
$$

**Method 2: Prime Power**
$$
\phi(36) = \phi(4) \times \phi(9) = 2 \times 6 = 12
$$

**Verification**: Integers 1-36 coprime to 36:
1, 5, 7, 11, 13, 17, 19, 23, 25, 29, 31, 35 → **12 numbers** ✓

## 5. Complexity Analysis

### 5.1 Time Complexity

| Method | Time | Use Case |
|--------|------|----------|
| Naive (counting GCDs) | O(n log n) | Educational |
| Product formula | O(√n) | Single query |
| Sieve | O(n log log n) | Multiple queries |

### 5.2 Space Complexity

| Method | Space |
|--------|-------|
| Product formula | O(1) |
| Sieve | O(n) |

## 6. Visual Representation

### 6.1 Totient Pattern

```
n:    1  2  3  4  5  6  7  8  9 10 11 12
φ(n): 1  1  2  2  4  2  6  4  6  4 10  4

Pattern observations:
- φ(p) = p-1 for prime p
- φ(2n) = φ(n) or 2φ(n)
- φ(p^k) = p^(k-1)(p-1)
```

### 6.2 Coprime Visualization for n=12

```
Numbers 1-12:  1  2  3  4  5  6  7  8  9 10 11 12
                ↓     ↓  ↓     ↓  ↓     ↓  ↓
Coprime to 12: 1     -  -  5  -  7  -  -  - 11  -

φ(12) = 4 (highlighted numbers)
```

## 7. Implementation

```python
from typing import List, Dict
from functools import lru_cache
import math


def euler_totient(n: int) -> int:
    """
    Compute Euler's totient function φ(n).
    
    Uses the product formula: φ(n) = n × ∏(1 - 1/p) for prime p | n
    
    Time: O(√n), Space: O(1)
    
    >>> euler_totient(1)
    1
    >>> euler_totient(10)
    4
    >>> euler_totient(36)
    12
    >>> euler_totient(97)  # Prime
    96
    """
    if n <= 0:
        raise ValueError("n must be positive")
    
    result = n
    
    # Check for factor 2
    if n % 2 == 0:
        result -= result // 2
        while n % 2 == 0:
            n //= 2
    
    # Check odd factors
    p = 3
    while p * p <= n:
        if n % p == 0:
            result -= result // p
            while n % p == 0:
                n //= p
        p += 2
    
    # If n is still > 1, it's a prime factor
    if n > 1:
        result -= result // n
    
    return result


def totient_sieve(n: int) -> List[int]:
    """
    Compute φ(k) for all k from 0 to n using sieve.
    
    Time: O(n log log n), Space: O(n)
    
    >>> totient_sieve(10)
    [0, 1, 1, 2, 2, 4, 2, 6, 4, 6, 4]
    """
    phi = list(range(n + 1))
    
    for i in range(2, n + 1):
        if phi[i] == i:  # i is prime
            for j in range(i, n + 1, i):
                phi[j] -= phi[j] // i
    
    return phi


def totient_sum_sieve(n: int) -> List[int]:
    """
    Compute Σφ(k) for k=1 to i, for all i from 0 to n.
    
    >>> totient_sum_sieve(10)
    [0, 1, 2, 4, 6, 10, 12, 18, 22, 28, 32]
    """
    phi = totient_sieve(n)
    prefix_sum = [0] * (n + 1)
    
    for i in range(1, n + 1):
        prefix_sum[i] = prefix_sum[i - 1] + phi[i]
    
    return prefix_sum


def count_coprimes_in_range(n: int, a: int, b: int) -> int:
    """
    Count integers in [a, b] that are coprime to n.
    
    Uses inclusion-exclusion principle.
    
    >>> count_coprimes_in_range(6, 1, 12)  # Coprime to 6: 1,5,7,11
    4
    """
    def count_coprime_up_to(n: int, limit: int) -> int:
        """Count integers in [1, limit] coprime to n."""
        if limit <= 0:
            return 0
        
        # Get prime factors of n
        factors = []
        temp = n
        d = 2
        while d * d <= temp:
            if temp % d == 0:
                factors.append(d)
                while temp % d == 0:
                    temp //= d
            d += 1
        if temp > 1:
            factors.append(temp)
        
        # Inclusion-exclusion
        count = 0
        for mask in range(1 << len(factors)):
            product = 1
            bits = bin(mask).count('1')
            
            for i in range(len(factors)):
                if mask & (1 << i):
                    product *= factors[i]
            
            # Add or subtract based on number of factors
            if bits % 2 == 1:
                count -= limit // product
            else:
                count += limit // product
        
        return count
    
    return count_coprime_up_to(n, b) - count_coprime_up_to(n, a - 1)


def reduced_residue_system(n: int) -> List[int]:
    """
    Return the reduced residue system modulo n.
    
    This is the set of integers in [1, n] coprime to n.
    
    >>> reduced_residue_system(12)
    [1, 5, 7, 11]
    >>> len(reduced_residue_system(100)) == euler_totient(100)
    True
    """
    return [k for k in range(1, n + 1) if math.gcd(k, n) == 1]


def carmichael_function(n: int) -> int:
    """
    Compute Carmichael's function λ(n).
    
    λ(n) is the smallest positive integer m such that
    a^m ≡ 1 (mod n) for all a coprime to n.
    
    >>> carmichael_function(8)  # λ(8) = 2, not φ(8) = 4
    2
    >>> carmichael_function(15)
    4
    """
    if n == 1:
        return 1
    
    # Factorize n
    factors = {}
    temp = n
    d = 2
    while d * d <= temp:
        while temp % d == 0:
            factors[d] = factors.get(d, 0) + 1
            temp //= d
        d += 1
    if temp > 1:
        factors[temp] = 1
    
    def lambda_prime_power(p: int, k: int) -> int:
        """Compute λ(p^k)."""
        if p == 2 and k >= 3:
            return (p ** (k - 1)) // 2
        else:
            return (p - 1) * (p ** (k - 1))
    
    result = 1
    for p, k in factors.items():
        lam = lambda_prime_power(p, k)
        result = result * lam // math.gcd(result, lam)  # LCM
    
    return result


@lru_cache(maxsize=None)
def totient_sum(n: int) -> int:
    """
    Compute Σφ(k) for k=1 to n using efficient formula.
    
    Uses the identity: Σφ(d) = n for d|n, inverted via Möbius.
    
    Time: O(n^(2/3)) with memoization
    
    >>> totient_sum(10)
    32
    >>> totient_sum(100)
    3044
    """
    if n <= 1:
        return n
    
    # Use identity: Σ_{k=1}^n φ(k) = (n*(n+1))/2 - Σ_{d=2}^n Σ_{k=1}^{n/d} φ(k)
    # Which simplifies using recursion
    
    result = n * (n + 1) // 2
    
    # Use block sieving for efficiency
    v = int(n ** 0.5)
    
    # Sum over d from 2 to v
    d = 2
    while d <= v:
        result -= totient_sum(n // d)
        d += 1
    
    # Sum over remaining values of n//d
    prev = n // (v + 1)
    for q in range(prev, 0, -1):
        d_start = n // (q + 1) + 1
        d_end = n // q
        if d_start <= d_end:
            result -= (d_end - d_start + 1) * totient_sum(q)
    
    return result
```

## 8. Applications

### 8.1 RSA Cryptography

$\phi(n)$ where $n = pq$ determines the private key:
$$
d \cdot e \equiv 1 \pmod{\phi(n)}
$$

### 8.2 Modular Exponentiation

Reduce exponent modulo $\phi(n)$:
$$
a^k \equiv a^{k \mod \phi(n)} \pmod{n} \quad \text{(when } \gcd(a, n) = 1\text{)}
$$

### 8.3 Primitive Roots

A primitive root modulo $n$ exists iff $n \in \{1, 2, 4, p^k, 2p^k\}$ for odd prime $p$.
Number of primitive roots = $\phi(\phi(n))$.

## 9. Real-World Software Engineering Applications

### 9.1 Industry Use Cases

1. **Cryptography**
   - RSA key generation
   - Diffie-Hellman
   - ElGamal encryption

2. **Competitive Programming**
   - Modular arithmetic
   - Counting problems

3. **Number Theory Research**
   - Prime distribution
   - Multiplicative functions

### 9.2 Production Example: RSA Key Analysis

```python
from typing import Tuple, Optional
import random


class RSAAnalyzer:
    """
    RSA key analysis using Euler's totient function.
    """
    
    @staticmethod
    def analyze_key_strength(n: int, e: int) -> dict:
        """
        Analyze RSA public key for potential weaknesses.
        
        Note: This requires knowing the factorization, which
        defeats the purpose of RSA security. For educational use.
        """
        # Try small factor attack
        small_factor = RSAAnalyzer._find_small_factor(n)
        if small_factor:
            p = small_factor
            q = n // p
            phi_n = (p - 1) * (q - 1)
            
            return {
                'factored': True,
                'p': p,
                'q': q,
                'phi_n': phi_n,
                'vulnerability': 'Small factor found'
            }
        
        return {
            'factored': False,
            'vulnerability': None,
            'bit_length': n.bit_length()
        }
    
    @staticmethod
    def _find_small_factor(n: int, limit: int = 100000) -> Optional[int]:
        """Try to find a small factor of n."""
        if n % 2 == 0:
            return 2
        
        for p in range(3, min(limit, int(n**0.5) + 1), 2):
            if n % p == 0:
                return p
        
        return None
    
    @staticmethod
    def verify_key_pair(n: int, e: int, d: int) -> bool:
        """
        Verify that (e, d) is a valid RSA key pair for modulus n.
        
        Uses the property: e*d ≡ 1 (mod λ(n))
        """
        # Test with random message
        for _ in range(10):
            m = random.randrange(2, n)
            c = pow(m, e, n)
            m_recovered = pow(c, d, n)
            if m != m_recovered:
                return False
        return True
    
    @staticmethod
    def compute_private_exponent(p: int, q: int, e: int) -> int:
        """
        Compute private exponent d given factorization.
        
        d ≡ e^(-1) (mod φ(n)) or (mod λ(n))
        """
        # Using φ(n) = (p-1)(q-1)
        phi_n = (p - 1) * (q - 1)
        
        # Extended GCD to find inverse
        def extended_gcd(a: int, b: int) -> Tuple[int, int, int]:
            if b == 0:
                return a, 1, 0
            g, x, y = extended_gcd(b, a % b)
            return g, y, x - (a // b) * y
        
        g, x, _ = extended_gcd(e, phi_n)
        if g != 1:
            raise ValueError("e and φ(n) are not coprime")
        
        return x % phi_n
    
    @staticmethod
    def totient_based_attack(n: int, phi_n: int) -> Tuple[int, int]:
        """
        Factor n given φ(n).
        
        Uses: n = pq and φ(n) = (p-1)(q-1) = pq - p - q + 1
        So: p + q = n - φ(n) + 1
        And: pq = n
        
        p and q are roots of: x² - (p+q)x + pq = 0
        """
        sum_pq = n - phi_n + 1
        
        # Quadratic formula
        discriminant = sum_pq ** 2 - 4 * n
        
        if discriminant < 0:
            raise ValueError("Invalid φ(n)")
        
        sqrt_disc = int(discriminant ** 0.5)
        if sqrt_disc * sqrt_disc != discriminant:
            raise ValueError("Discriminant is not a perfect square")
        
        p = (sum_pq + sqrt_disc) // 2
        q = (sum_pq - sqrt_disc) // 2
        
        if p * q != n:
            raise ValueError("Factorization failed")
        
        return (p, q) if p > q else (q, p)


# Demo: Show how knowing φ(n) breaks RSA
p, q = 61, 53
n = p * q
phi_n = (p - 1) * (q - 1)
e = 17

print(f"RSA parameters: n={n}, e={e}")
print(f"Secret: p={p}, q={q}, φ(n)={phi_n}")

# Compute d
analyzer = RSAAnalyzer()
d = analyzer.compute_private_exponent(p, q, e)
print(f"Private exponent d={d}")

# Verify
print(f"Key pair valid: {analyzer.verify_key_pair(n, e, d)}")

# Attack demonstration: factor n from φ(n)
recovered_p, recovered_q = analyzer.totient_based_attack(n, phi_n)
print(f"Recovered factors: p={recovered_p}, q={recovered_q}")
```

### 9.3 Cyclic Group Analysis

```python
def analyze_multiplicative_group(n: int) -> dict:
    """
    Analyze the multiplicative group (Z/nZ)*.
    
    This group has φ(n) elements.
    """
    import math
    
    phi = euler_totient(n)
    elements = reduced_residue_system(n)
    
    # Find orders of each element
    orders = {}
    for a in elements:
        order = 1
        current = a
        while current % n != 1:
            current = (current * a) % n
            order += 1
        orders[a] = order
    
    # Find primitive roots (generators)
    primitive_roots = [a for a, ord in orders.items() if ord == phi]
    
    # Analyze subgroups
    divisors_of_phi = [d for d in range(1, phi + 1) if phi % d == 0]
    subgroup_sizes = {d: sum(1 for o in orders.values() if d % o == 0) for d in divisors_of_phi}
    
    return {
        'n': n,
        'group_order': phi,
        'elements': elements,
        'element_orders': orders,
        'is_cyclic': len(primitive_roots) > 0,
        'primitive_roots': primitive_roots,
        'num_primitive_roots': len(primitive_roots),
        'theoretical_num_primitive_roots': euler_totient(phi) if primitive_roots else 0,
        'subgroup_orders': divisors_of_phi
    }


# Analyze (Z/15Z)*
result = analyze_multiplicative_group(15)
print(f"Group (Z/15Z)*:")
print(f"  Order: {result['group_order']}")
print(f"  Elements: {result['elements']}")
print(f"  Is cyclic: {result['is_cyclic']}")
print(f"  Element orders: {result['element_orders']}")
```

## 10. Related Functions

| Function | Definition | Use |
|----------|------------|-----|
| **φ(n)** | Count coprimes | RSA, group order |
| **λ(n)** | Carmichael | Minimal exponent |
| **μ(n)** | Möbius | Inversion formulas |
| **σ(n)** | Sum of divisors | Perfect numbers |

## 11. Edge Cases

| Input | Output | Notes |
|-------|--------|-------|
| n = 1 | 1 | gcd(1,1) = 1 |
| n = 2 | 1 | Only 1 is coprime |
| Prime p | p - 1 | All smaller are coprime |
| Prime power p^k | p^(k-1)(p-1) | Formula |

## 12. References

- [Wikipedia: Euler's Totient Function](https://en.wikipedia.org/wiki/Euler%27s_totient_function)
- Hardy, G.H., Wright, E.M. "An Introduction to the Theory of Numbers"
- [OEIS A000010](https://oeis.org/A000010)
