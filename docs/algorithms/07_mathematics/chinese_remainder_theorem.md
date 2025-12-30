# Chinese Remainder Theorem (CRT)

## Overview
- **Category**: Number Theory
- **Complexity**: Time: O(n log M) | Space: O(n)
- **Type**: System of congruences solver
- **Source File**: [maths/chinese_remainder_theorem.py](../../../maths/chinese_remainder_theorem.py)

## 1. Mathematical Foundation

### 1.1 Problem Statement

Given pairwise coprime moduli $m_1, m_2, \ldots, m_k$ and remainders $a_1, a_2, \ldots, a_k$, find $x$ such that:

$$
\begin{cases}
x \equiv a_1 \pmod{m_1} \\
x \equiv a_2 \pmod{m_2} \\
\vdots \\
x \equiv a_k \pmod{m_k}
\end{cases}
$$

### 1.2 Existence and Uniqueness Theorem

**Theorem (Chinese Remainder Theorem)**: If $m_1, m_2, \ldots, m_k$ are pairwise coprime, then the system has a unique solution modulo $M = m_1 \cdot m_2 \cdots m_k$.

### 1.3 Constructive Solution

Let $M = \prod_{i=1}^{k} m_i$ and $M_i = M / m_i$.

Since $\gcd(M_i, m_i) = 1$, there exists $y_i$ such that:
$$
M_i \cdot y_i \equiv 1 \pmod{m_i}
$$

The solution is:
$$
x = \sum_{i=1}^{k} a_i \cdot M_i \cdot y_i \pmod{M}
$$

### 1.4 Proof of Correctness

For each $j$:
$$
x \equiv \sum_{i=1}^{k} a_i \cdot M_i \cdot y_i \pmod{m_j}
$$

Since $M_i \equiv 0 \pmod{m_j}$ for $i \neq j$:
$$
x \equiv a_j \cdot M_j \cdot y_j \equiv a_j \cdot 1 \equiv a_j \pmod{m_j}
$$

### 1.5 Non-Coprime Extension

For non-coprime moduli, solution exists if and only if:
$$
a_i \equiv a_j \pmod{\gcd(m_i, m_j)} \quad \forall i, j
$$

## 2. Algorithm Description

### 2.1 Standard CRT Algorithm

1. Compute $M = \prod m_i$
2. For each $i$: compute $M_i = M / m_i$
3. For each $i$: find $y_i = M_i^{-1} \pmod{m_i}$ using Extended GCD
4. Compute $x = \sum a_i \cdot M_i \cdot y_i \pmod{M}$

### 2.2 Iterative (Garner's) Algorithm

Incrementally combine two congruences at a time:
1. Start with $x \equiv a_1 \pmod{m_1}$
2. Combine with next congruence
3. Repeat until all processed

## 3. Pseudocode

```
ALGORITHM CRT-Standard(a[], m[], k)
    INPUT: Remainders a[1..k], coprime moduli m[1..k]
    OUTPUT: x such that x ≡ a[i] (mod m[i]) for all i
    
    M ← ∏ᵢ m[i]
    x ← 0
    
    for i ← 1 to k do
        Mᵢ ← M / m[i]
        yᵢ ← ModularInverse(Mᵢ, m[i])  // Using Extended GCD
        x ← x + a[i] × Mᵢ × yᵢ
    
    return x mod M

ALGORITHM CRT-Iterative(a[], m[], k)
    INPUT: Remainders a[1..k], coprime moduli m[1..k]
    OUTPUT: x such that x ≡ a[i] (mod m[i]) for all i
    
    x ← a[1]
    mod ← m[1]
    
    for i ← 2 to k do
        // Solve: x ≡ x (mod mod), x ≡ a[i] (mod m[i])
        // x = x + mod × t where mod × t ≡ a[i] - x (mod m[i])
        
        diff ← (a[i] - x) mod m[i]
        if diff < 0 then diff ← diff + m[i]
        
        inv ← ModularInverse(mod, m[i])
        t ← (diff × inv) mod m[i]
        
        x ← x + mod × t
        mod ← mod × m[i]
    
    return x mod mod

ALGORITHM CRT-TwoCongruences(a₁, m₁, a₂, m₂)
    INPUT: Two congruences x ≡ a₁ (mod m₁), x ≡ a₂ (mod m₂)
    OUTPUT: x ≡ a (mod m₁×m₂) or ERROR if no solution
    
    (g, p, q) ← ExtendedGCD(m₁, m₂)
    
    if (a₂ - a₁) mod g ≠ 0 then
        ERROR "No solution exists"
    
    lcm ← m₁ × m₂ / g
    x ← (a₁ + m₁ × p × ((a₂ - a₁) / g)) mod lcm
    
    return x
```

## 4. Step-by-Step Example

### Example: Solve the system

$$
\begin{cases}
x \equiv 2 \pmod{3} \\
x \equiv 3 \pmod{5} \\
x \equiv 2 \pmod{7}
\end{cases}
$$

**Step 1**: Compute products
- $M = 3 \times 5 \times 7 = 105$
- $M_1 = 105/3 = 35$
- $M_2 = 105/5 = 21$
- $M_3 = 105/7 = 15$

**Step 2**: Compute modular inverses
- $35 \cdot y_1 \equiv 1 \pmod{3}$ → $2 \cdot y_1 \equiv 1$ → $y_1 = 2$
- $21 \cdot y_2 \equiv 1 \pmod{5}$ → $1 \cdot y_2 \equiv 1$ → $y_2 = 1$
- $15 \cdot y_3 \equiv 1 \pmod{7}$ → $1 \cdot y_3 \equiv 1$ → $y_3 = 1$

**Step 3**: Compute solution
$$
x = 2 \cdot 35 \cdot 2 + 3 \cdot 21 \cdot 1 + 2 \cdot 15 \cdot 1 = 140 + 63 + 30 = 233
$$
$$
x \equiv 233 \pmod{105} \equiv 23
$$

**Verification**:
- $23 = 7 \cdot 3 + 2$ ✓
- $23 = 4 \cdot 5 + 3$ ✓
- $23 = 3 \cdot 7 + 2$ ✓

## 5. Complexity Analysis

### 5.1 Time Complexity

| Operation | Cost |
|-----------|------|
| Product $M = \prod m_i$ | $O(k \cdot \log M)$ |
| Each modular inverse | $O(\log m_i)$ |
| Final combination | $O(k \cdot \log M)$ |
| **Total** | $O(k \cdot \log M)$ |

### 5.2 Space Complexity

$O(k)$ to store arrays

### 5.3 Numerical Stability

For large $M$, use arbitrary precision or compute modulo each $m_i$ separately.

## 6. Visual Representation

### 6.1 Residue Class Intersection

```
Numbers ≡ 2 (mod 3): 2, 5, 8, 11, 14, 17, 20, [23], 26, 29, ...
Numbers ≡ 3 (mod 5): 3, 8, 13, 18, [23], 28, 33, 38, 43, ...
Numbers ≡ 2 (mod 7): 2, 9, 16, [23], 30, 37, 44, 51, 58, ...

Intersection: 23, 128, 233, ... (period = 105)
```

### 6.2 Algorithm Flow

```mermaid
graph TD
    A[Input: a[], m[]] --> B[Compute M = ∏mᵢ]
    B --> C[For each i: Mᵢ = M/mᵢ]
    C --> D[For each i: yᵢ = Mᵢ⁻¹ mod mᵢ]
    D --> E[x = Σ aᵢ·Mᵢ·yᵢ]
    E --> F[Return x mod M]
```

## 7. Implementation

```python
from typing import List, Tuple, Optional
from functools import reduce


def extended_gcd(a: int, b: int) -> Tuple[int, int, int]:
    """
    Extended Euclidean Algorithm.
    Returns (gcd, x, y) such that a*x + b*y = gcd.
    """
    if b == 0:
        return a, 1, 0
    g, x, y = extended_gcd(b, a % b)
    return g, y, x - (a // b) * y


def mod_inverse(a: int, m: int) -> int:
    """
    Compute modular multiplicative inverse of a modulo m.
    
    >>> mod_inverse(35, 3)
    2
    >>> mod_inverse(21, 5)
    1
    """
    g, x, _ = extended_gcd(a % m, m)
    if g != 1:
        raise ValueError(f"Inverse doesn't exist: gcd({a}, {m}) = {g}")
    return x % m


def chinese_remainder_theorem(
    remainders: List[int],
    moduli: List[int]
) -> int:
    """
    Solve system of congruences using CRT.
    
    Args:
        remainders: List of remainders [a₁, a₂, ..., aₖ]
        moduli: List of pairwise coprime moduli [m₁, m₂, ..., mₖ]
    
    Returns:
        x such that x ≡ aᵢ (mod mᵢ) for all i
    
    >>> chinese_remainder_theorem([2, 3, 2], [3, 5, 7])
    23
    >>> chinese_remainder_theorem([1, 2, 3], [2, 3, 5])
    23
    """
    if len(remainders) != len(moduli):
        raise ValueError("Arrays must have same length")
    
    # Compute product of all moduli
    M = reduce(lambda x, y: x * y, moduli)
    
    x = 0
    for a_i, m_i in zip(remainders, moduli):
        M_i = M // m_i
        y_i = mod_inverse(M_i, m_i)
        x += a_i * M_i * y_i
    
    return x % M


def crt_iterative(
    remainders: List[int],
    moduli: List[int]
) -> int:
    """
    Iterative CRT implementation (Garner's algorithm).
    
    Combines congruences two at a time.
    
    >>> crt_iterative([2, 3, 2], [3, 5, 7])
    23
    """
    x = remainders[0]
    mod = moduli[0]
    
    for i in range(1, len(remainders)):
        a_i, m_i = remainders[i], moduli[i]
        
        # Solve: x ≡ x (mod mod), x ≡ a_i (mod m_i)
        diff = (a_i - x) % m_i
        inv = mod_inverse(mod, m_i)
        t = (diff * inv) % m_i
        
        x = x + mod * t
        mod = mod * m_i
    
    return x % mod


def crt_two_congruences(
    a1: int, m1: int,
    a2: int, m2: int
) -> Optional[Tuple[int, int]]:
    """
    Solve two congruences: x ≡ a1 (mod m1), x ≡ a2 (mod m2)
    
    Works even if m1, m2 are not coprime.
    
    Returns:
        (x, lcm(m1, m2)) or None if no solution
    
    >>> crt_two_congruences(2, 3, 3, 5)
    (8, 15)
    >>> crt_two_congruences(1, 6, 4, 9)
    (13, 18)
    >>> crt_two_congruences(1, 6, 2, 9) is None  # No solution
    True
    """
    g, p, _ = extended_gcd(m1, m2)
    
    # Check solvability
    if (a2 - a1) % g != 0:
        return None
    
    lcm = m1 * m2 // g
    x = (a1 + m1 * p * ((a2 - a1) // g)) % lcm
    
    return (x if x >= 0 else x + lcm, lcm)


def crt_general(
    remainders: List[int],
    moduli: List[int]
) -> Optional[Tuple[int, int]]:
    """
    General CRT for possibly non-coprime moduli.
    
    Returns:
        (x, lcm) where x is the smallest non-negative solution,
        or None if no solution exists.
    
    >>> crt_general([2, 3, 2], [3, 5, 7])
    (23, 105)
    >>> crt_general([1, 4], [6, 9])
    (13, 18)
    """
    if not remainders:
        return (0, 1)
    
    x, mod = remainders[0] % moduli[0], moduli[0]
    
    for a, m in zip(remainders[1:], moduli[1:]):
        result = crt_two_congruences(x, mod, a, m)
        if result is None:
            return None
        x, mod = result
    
    return (x, mod)


def solve_simultaneous_congruences(
    equations: List[Tuple[int, int]]
) -> Optional[int]:
    """
    Solve: x ≡ aᵢ (mod mᵢ) given as list of (aᵢ, mᵢ) tuples.
    
    >>> solve_simultaneous_congruences([(2, 3), (3, 5), (2, 7)])
    23
    """
    remainders = [a for a, _ in equations]
    moduli = [m for _, m in equations]
    result = crt_general(remainders, moduli)
    return result[0] if result else None


def crt_reconstruction(
    values: List[int],
    primes: List[int]
) -> int:
    """
    Reconstruct integer from its residues modulo distinct primes.
    
    Used in multi-prime arithmetic and cryptography.
    
    >>> crt_reconstruction([2, 3, 5], [3, 5, 7])  # What number ≡ 2,3,5 mod 3,5,7?
    68
    """
    return chinese_remainder_theorem(values, primes)
```

## 8. Applications

### 8.1 RSA with CRT

Decrypt faster using:
$$
m_p = c^{d \mod (p-1)} \mod p, \quad m_q = c^{d \mod (q-1)} \mod q
$$
Then combine using CRT.

### 8.2 Big Integer Arithmetic

Represent large integers as residues modulo small primes.
Perform arithmetic on residues, then reconstruct.

### 8.3 Lagrange Interpolation

CRT is equivalent to Lagrange interpolation over integers.

## 9. Real-World Software Engineering Applications

### 9.1 Industry Use Cases

1. **Cryptography**
   - RSA decryption speedup (4× faster)
   - Secret sharing schemes
   - Homomorphic encryption

2. **Computer Algebra**
   - Multi-modular arithmetic
   - Polynomial GCD
   - Integer factorization

3. **Distributed Computing**
   - Data partitioning
   - Load balancing

4. **Coding Theory**
   - Error correction codes
   - Reed-Solomon decoding

### 9.2 Production Example: RSA-CRT Decryption

```python
import random
from typing import Tuple


class RSA_CRT:
    """
    RSA implementation with CRT optimization for decryption.
    """
    
    def __init__(self, bits: int = 2048):
        self.bits = bits
        self.public_key = None
        self.private_key = None
        self.p = None
        self.q = None
        self.dp = None
        self.dq = None
        self.q_inv = None
    
    def _mod_exp(self, base: int, exp: int, mod: int) -> int:
        """Fast modular exponentiation."""
        result = 1
        base = base % mod
        while exp > 0:
            if exp & 1:
                result = (result * base) % mod
            exp >>= 1
            base = (base * base) % mod
        return result
    
    def _is_prime(self, n: int, k: int = 40) -> bool:
        """Miller-Rabin primality test."""
        if n < 2:
            return False
        if n == 2 or n == 3:
            return True
        if n % 2 == 0:
            return False
        
        r, d = 0, n - 1
        while d % 2 == 0:
            r += 1
            d //= 2
        
        for _ in range(k):
            a = random.randrange(2, n - 1)
            x = self._mod_exp(a, d, n)
            if x == 1 or x == n - 1:
                continue
            for _ in range(r - 1):
                x = self._mod_exp(x, 2, n)
                if x == n - 1:
                    break
            else:
                return False
        return True
    
    def _generate_prime(self, bits: int) -> int:
        """Generate random prime."""
        while True:
            candidate = random.getrandbits(bits) | (1 << bits - 1) | 1
            if self._is_prime(candidate):
                return candidate
    
    def _extended_gcd(self, a: int, b: int) -> Tuple[int, int, int]:
        if b == 0:
            return a, 1, 0
        g, x, y = self._extended_gcd(b, a % b)
        return g, y, x - (a // b) * y
    
    def _mod_inverse(self, a: int, m: int) -> int:
        g, x, _ = self._extended_gcd(a, m)
        return x % m
    
    def generate_keys(self) -> None:
        """Generate RSA keys with CRT components."""
        self.p = self._generate_prime(self.bits // 2)
        self.q = self._generate_prime(self.bits // 2)
        
        n = self.p * self.q
        phi_n = (self.p - 1) * (self.q - 1)
        
        e = 65537
        d = self._mod_inverse(e, phi_n)
        
        # CRT components
        self.dp = d % (self.p - 1)
        self.dq = d % (self.q - 1)
        self.q_inv = self._mod_inverse(self.q, self.p)
        
        self.public_key = (e, n)
        self.private_key = d
    
    def encrypt(self, message: int) -> int:
        """Standard RSA encryption."""
        e, n = self.public_key
        return self._mod_exp(message, e, n)
    
    def decrypt_standard(self, ciphertext: int) -> int:
        """Standard RSA decryption (slow)."""
        n = self.p * self.q
        return self._mod_exp(ciphertext, self.private_key, n)
    
    def decrypt_crt(self, ciphertext: int) -> int:
        """
        RSA decryption using CRT (~4× faster).
        
        Instead of computing c^d mod n directly,
        compute mod p and mod q separately, then combine.
        """
        # Compute partial results
        m1 = self._mod_exp(ciphertext, self.dp, self.p)
        m2 = self._mod_exp(ciphertext, self.dq, self.q)
        
        # CRT combination
        h = (self.q_inv * (m1 - m2)) % self.p
        m = m2 + h * self.q
        
        return m
    
    def benchmark(self, iterations: int = 100) -> dict:
        """Compare standard vs CRT decryption speed."""
        import time
        
        # Generate test ciphertext
        message = random.randrange(2, self.p * self.q - 1)
        ciphertext = self.encrypt(message)
        
        # Time standard decryption
        start = time.time()
        for _ in range(iterations):
            m1 = self.decrypt_standard(ciphertext)
        standard_time = time.time() - start
        
        # Time CRT decryption
        start = time.time()
        for _ in range(iterations):
            m2 = self.decrypt_crt(ciphertext)
        crt_time = time.time() - start
        
        # Verify correctness
        assert m1 == m2 == message
        
        return {
            'standard_time': standard_time,
            'crt_time': crt_time,
            'speedup': standard_time / crt_time,
            'iterations': iterations
        }


# Benchmark
rsa = RSA_CRT(bits=1024)
rsa.generate_keys()

results = rsa.benchmark(iterations=100)
print(f"Standard decryption: {results['standard_time']:.3f}s")
print(f"CRT decryption: {results['crt_time']:.3f}s")
print(f"Speedup: {results['speedup']:.2f}x")
```

### 9.3 Multi-Modular Arithmetic

```python
from typing import List


class MultiModularInteger:
    """
    Represent integers using CRT representation for fast arithmetic.
    
    Uses residues modulo several small primes.
    """
    
    # First 10 primes
    PRIMES = [2, 3, 5, 7, 11, 13, 17, 19, 23, 29]
    
    def __init__(self, value: int = 0, residues: List[int] = None):
        if residues is not None:
            self.residues = residues
        else:
            self.residues = [value % p for p in self.PRIMES]
    
    @classmethod
    def from_int(cls, value: int) -> 'MultiModularInteger':
        return cls(value)
    
    def to_int(self) -> int:
        """Reconstruct integer using CRT."""
        return chinese_remainder_theorem(self.residues, self.PRIMES)
    
    def __add__(self, other: 'MultiModularInteger') -> 'MultiModularInteger':
        """Add in O(k) instead of O(log n) for large integers."""
        new_residues = [
            (r1 + r2) % p 
            for r1, r2, p in zip(self.residues, other.residues, self.PRIMES)
        ]
        return MultiModularInteger(residues=new_residues)
    
    def __sub__(self, other: 'MultiModularInteger') -> 'MultiModularInteger':
        new_residues = [
            (r1 - r2) % p 
            for r1, r2, p in zip(self.residues, other.residues, self.PRIMES)
        ]
        return MultiModularInteger(residues=new_residues)
    
    def __mul__(self, other: 'MultiModularInteger') -> 'MultiModularInteger':
        """Multiply in O(k) instead of O(log² n) for large integers."""
        new_residues = [
            (r1 * r2) % p 
            for r1, r2, p in zip(self.residues, other.residues, self.PRIMES)
        ]
        return MultiModularInteger(residues=new_residues)
    
    def __repr__(self) -> str:
        return f"MultiModularInteger({self.to_int()})"


# Example: Large number arithmetic
a = MultiModularInteger.from_int(12345)
b = MultiModularInteger.from_int(67890)

c = a * b
print(f"{a.to_int()} × {b.to_int()} = {c.to_int()}")
print(f"Verification: {12345 * 67890}")
```

## 10. Variants and Extensions

### 10.1 Generalized CRT

For non-coprime moduli, solution exists iff $a_i \equiv a_j \pmod{\gcd(m_i, m_j)}$.

### 10.2 CRT over Polynomials

Same algorithm works for polynomials over a field.

### 10.3 Partial CRT

When only some residues are known, can still narrow down possibilities.

## 11. Edge Cases

| Input | Output | Notes |
|-------|--------|-------|
| Single congruence | Same as input | Trivial |
| m_i = 1 for some i | a_i must be 0 | Constraint |
| Non-coprime moduli | May have no solution | Use general CRT |
| All a_i = 0 | x = 0 | Trivial solution |

## 12. References

- [Wikipedia: Chinese Remainder Theorem](https://en.wikipedia.org/wiki/Chinese_remainder_theorem)
- Knuth, D.E. "The Art of Computer Programming, Volume 2"
- Shoup, V. "A Computational Introduction to Number Theory and Algebra"
