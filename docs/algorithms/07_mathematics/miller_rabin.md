# Miller-Rabin Primality Test

## Overview
- **Category**: Number Theory / Cryptography
- **Complexity**: Time: O(k log³ n) | Space: O(1)
- **Type**: Probabilistic algorithm
- **Source File**: [maths/miller_rabin.py](../../../maths/miller_rabin.py)

## 1. Mathematical Foundation

### 1.1 Fermat's Little Theorem

For prime $p$ and $\gcd(a, p) = 1$:
$$
a^{p-1} \equiv 1 \pmod{p}
$$

**Contrapositive**: If $a^{n-1} \not\equiv 1 \pmod{n}$, then $n$ is composite.

### 1.2 Fermat Pseudoprimes

Some composite numbers pass Fermat's test for certain bases (Carmichael numbers pass for all bases coprime to them).

**Example**: 561 = 3 × 11 × 17 is a Carmichael number.

### 1.3 Miller-Rabin Strengthening

Write $n - 1 = 2^s \cdot d$ where $d$ is odd.

For prime $n$, one of these must hold:
1. $a^d \equiv 1 \pmod{n}$, OR
2. $a^{2^r \cdot d} \equiv -1 \pmod{n}$ for some $0 \leq r < s$

**Why it works**: In a prime field, the only square roots of 1 are ±1.

### 1.4 Strong Pseudoprimes

A composite $n$ is a **strong pseudoprime** to base $a$ if it passes Miller-Rabin test for $a$.

**Key theorem**: At most 1/4 of bases $a$ are witnesses for a composite $n$ being "prime-like".

### 1.5 Error Probability

After $k$ independent rounds:
- If composite: detected with probability $\geq 1 - 4^{-k}$
- If prime: always declared prime

## 2. Algorithm Description

### 2.1 Intuition

1. Factor out powers of 2 from $n-1$
2. For random base $a$:
   - Compute $x = a^d \mod n$
   - If $x = 1$ or $x = n-1$: inconclusive
   - Square repeatedly; look for $-1$ before reaching $1$
3. If we get $1$ without seeing $-1$: composite!

### 2.2 Witness vs Liar

- **Witness**: Base $a$ that proves $n$ is composite
- **Liar**: Base $a$ that makes composite $n$ appear prime

## 3. Pseudocode

```
ALGORITHM Miller-Rabin(n, k)
    INPUT: Odd integer n > 2, security parameter k
    OUTPUT: "composite" or "probably prime"
    
    // Factor out powers of 2 from n-1
    Write n - 1 = 2^s × d with d odd
    
    REPEAT k times:
        a ← random integer in [2, n-2]
        
        if IsWitness(a, n, s, d) then
            return "composite"
    
    return "probably prime"

ALGORITHM IsWitness(a, n, s, d)
    // Check if a is a witness that n is composite
    
    x ← a^d mod n  // Using modular exponentiation
    
    if x = 1 OR x = n - 1 then
        return FALSE  // Inconclusive
    
    for r ← 1 to s - 1 do
        x ← x² mod n
        
        if x = n - 1 then
            return FALSE  // Inconclusive
        
        if x = 1 then
            return TRUE  // Found a nontrivial sqrt of 1
    
    return TRUE  // Never reached -1, so composite
```

## 4. Step-by-Step Example

### Example: Test if n = 221 is prime

**Step 1**: Factor $n - 1 = 220 = 2^2 \times 55$, so $s = 2$, $d = 55$

**Step 2**: Choose $a = 174$

Compute $x = 174^{55} \mod 221$:
- $174^{55} \mod 221 = 47$
- $47 \neq 1$ and $47 \neq 220$, so continue

**Step 3**: Square (r = 1)
- $47^2 = 2209 \equiv 220 \pmod{221}$
- $220 = n - 1$, so INCONCLUSIVE

**Step 4**: Try another base $a = 137$
- $137^{55} \mod 221 = 188$
- $188^2 = 35344 \equiv 205 \pmod{221}$
- $205^2 = 42025 \equiv 1 \pmod{221}$

We got 1 without passing through -1!

**Conclusion**: 221 is COMPOSITE (221 = 13 × 17)

## 5. Complexity Analysis

### 5.1 Time Complexity

**Per round**:
- Modular exponentiation: $O(\log^2 n)$ bit operations (naive)
- With fast multiplication: $O(\log n \cdot M(\log n))$ where $M$ is multiplication complexity

**Total for k rounds**: $O(k \log^3 n)$ with schoolbook multiplication

### 5.2 Space Complexity

$O(1)$ auxiliary space (O(\log n) for storing numbers)

### 5.3 Error Analysis

| Rounds (k) | Error Probability | Bits of Security |
|------------|------------------|------------------|
| 1 | ≤ 1/4 | 2 |
| 10 | ≤ 10⁻⁶ | 20 |
| 40 | ≤ 10⁻²⁴ | 80 |
| 64 | ≤ 2⁻¹²⁸ | 128 |

## 6. Visual Representation

### 6.1 Decision Tree

```
                n - 1 = 2^s × d
                      |
                x₀ = a^d mod n
                      |
            ┌────────┴────────┐
            │                  │
        x₀ = 1?            x₀ = n-1?
           │                   │
         YES ──► Maybe Prime ◄── YES
           │                   │
          NO                  NO
           │                   │
           ▼                   ▼
        x₁ = x₀²          Continue...
           │
    ┌──────┴──────┐
    │              │
x₁ = n-1?      x₁ = 1?
    │              │
  YES ──► Maybe   YES ──► COMPOSITE!
    │           (found nontrivial sqrt)
   NO
    │
Continue squaring...
```

### 6.2 Square Root Chain

```
For prime p: Starting from a^d, squaring gives:

a^d → a^(2d) → a^(4d) → ... → a^(n-1) = 1

The sequence of values mod p must be:
  ... → -1 → 1 → 1 → 1

For composite n, we might see:
  x → 1 (where x ≠ ±1) — IMPOSSIBLE for prime!
```

## 7. Implementation

```python
import random
from typing import Tuple


def decompose(n: int) -> Tuple[int, int]:
    """
    Write n-1 as 2^s * d where d is odd.
    
    >>> decompose(221)
    (2, 55)
    >>> decompose(561)
    (4, 35)
    """
    s = 0
    d = n - 1
    while d % 2 == 0:
        s += 1
        d //= 2
    return s, d


def mod_exp(base: int, exp: int, mod: int) -> int:
    """Fast modular exponentiation."""
    result = 1
    base = base % mod
    while exp > 0:
        if exp & 1:
            result = (result * base) % mod
        exp >>= 1
        base = (base * base) % mod
    return result


def is_witness(a: int, n: int, s: int, d: int) -> bool:
    """
    Check if a is a witness that n is composite.
    
    >>> is_witness(2, 221, 2, 55)  # 221 = 13 * 17
    True
    >>> is_witness(2, 7, 1, 3)  # 7 is prime
    False
    """
    x = mod_exp(a, d, n)
    
    if x == 1 or x == n - 1:
        return False  # Inconclusive
    
    for _ in range(s - 1):
        x = (x * x) % n
        if x == n - 1:
            return False  # Inconclusive
        if x == 1:
            return True  # Found nontrivial sqrt of 1
    
    return True  # Never reached -1


def miller_rabin(n: int, k: int = 40) -> bool:
    """
    Miller-Rabin primality test.
    
    Args:
        n: Number to test
        k: Number of rounds (more = higher confidence)
    
    Returns:
        True if probably prime, False if definitely composite.
    
    >>> miller_rabin(7)
    True
    >>> miller_rabin(11)
    True
    >>> miller_rabin(221)  # 13 * 17
    False
    >>> miller_rabin(561)  # Carmichael number
    False
    >>> miller_rabin(104729)  # 10000th prime
    True
    >>> miller_rabin(2)
    True
    """
    # Handle small cases
    if n < 2:
        return False
    if n == 2 or n == 3:
        return True
    if n % 2 == 0:
        return False
    
    # Decompose n - 1
    s, d = decompose(n)
    
    # Run k rounds
    for _ in range(k):
        a = random.randrange(2, n - 1)
        if is_witness(a, n, s, d):
            return False
    
    return True


def miller_rabin_deterministic(n: int) -> bool:
    """
    Deterministic Miller-Rabin for n < 3,317,044,064,679,887,385,961,981.
    
    Uses specific witness sets that are known to be sufficient.
    
    >>> miller_rabin_deterministic(7)
    True
    >>> miller_rabin_deterministic(221)
    False
    >>> miller_rabin_deterministic(2147483647)  # Mersenne prime
    True
    """
    if n < 2:
        return False
    if n == 2 or n == 3:
        return True
    if n % 2 == 0:
        return False
    
    s, d = decompose(n)
    
    # Witness sets for different ranges
    if n < 2047:
        witnesses = [2]
    elif n < 1373653:
        witnesses = [2, 3]
    elif n < 9080191:
        witnesses = [31, 73]
    elif n < 25326001:
        witnesses = [2, 3, 5]
    elif n < 3215031751:
        witnesses = [2, 3, 5, 7]
    elif n < 4759123141:
        witnesses = [2, 7, 61]
    elif n < 1122004669633:
        witnesses = [2, 13, 23, 1662803]
    elif n < 2152302898747:
        witnesses = [2, 3, 5, 7, 11]
    elif n < 3474749660383:
        witnesses = [2, 3, 5, 7, 11, 13]
    elif n < 341550071728321:
        witnesses = [2, 3, 5, 7, 11, 13, 17]
    else:
        witnesses = [2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37]
    
    for a in witnesses:
        if a >= n:
            continue
        if is_witness(a, n, s, d):
            return False
    
    return True


def generate_prime(bits: int, k: int = 40) -> int:
    """
    Generate a random prime number with specified bit length.
    
    >>> p = generate_prime(512)
    >>> miller_rabin(p)
    True
    >>> p.bit_length()
    512
    """
    while True:
        # Generate random odd number of correct size
        candidate = random.getrandbits(bits) | (1 << bits - 1) | 1
        if miller_rabin(candidate, k):
            return candidate


def next_prime(n: int) -> int:
    """
    Find the smallest prime >= n.
    
    >>> next_prime(100)
    101
    >>> next_prime(1000)
    1009
    """
    if n <= 2:
        return 2
    if n % 2 == 0:
        n += 1
    
    while not miller_rabin(n):
        n += 2
    
    return n
```

## 8. Applications

### 8.1 Cryptographic Key Generation

RSA requires large primes (2048+ bits). Miller-Rabin efficiently tests candidates.

### 8.2 Primality Certificates

While Miller-Rabin doesn't prove primality, it's used as a fast filter before deterministic tests.

### 8.3 Factorization Algorithms

Pollard's rho and other methods use primality testing as a subroutine.

## 9. Real-World Software Engineering Applications

### 9.1 Industry Use Cases

1. **TLS/SSL Certificate Generation**
   - OpenSSL uses Miller-Rabin
   - Default: 40-80 rounds

2. **SSH Key Generation**
   - RSA key generation
   - DSA parameter generation

3. **Blockchain**
   - Key pair generation
   - Parameter validation

4. **Random Number Libraries**
   - Python's `secrets` module
   - Java's `BigInteger.probablePrime()`

### 9.2 Production Example: Secure Prime Generator

```python
import secrets
import hashlib
from typing import Optional


class SecurePrimeGenerator:
    """
    Cryptographically secure prime number generator.
    """
    
    def __init__(self, security_level: int = 128):
        """
        Initialize with security level in bits.
        
        Security Level -> Miller-Rabin rounds:
        80 bits -> 20 rounds
        128 bits -> 40 rounds
        256 bits -> 80 rounds
        """
        self.security_level = security_level
        self.rounds = security_level // 2  # Error prob ≤ 4^(-rounds)
    
    def _get_random_bits(self, bits: int) -> int:
        """Generate cryptographically secure random bits."""
        return secrets.randbits(bits)
    
    def _miller_rabin(self, n: int) -> bool:
        """Miller-Rabin with cryptographically secure randomness."""
        if n < 2:
            return False
        if n == 2 or n == 3:
            return True
        if n % 2 == 0:
            return False
        
        # Decompose n - 1 = 2^s * d
        s, d = 0, n - 1
        while d % 2 == 0:
            s += 1
            d //= 2
        
        # Run rounds with secure random witnesses
        for _ in range(self.rounds):
            a = secrets.randbelow(n - 3) + 2  # a in [2, n-2]
            
            x = pow(a, d, n)
            
            if x == 1 or x == n - 1:
                continue
            
            for _ in range(s - 1):
                x = pow(x, 2, n)
                if x == n - 1:
                    break
                if x == 1:
                    return False
            else:
                return False
        
        return True
    
    def generate_prime(self, bits: int) -> int:
        """
        Generate a random prime of specified bit length.
        
        Uses rejection sampling with Miller-Rabin testing.
        """
        attempts = 0
        while True:
            attempts += 1
            
            # Generate random odd number with correct bit length
            candidate = self._get_random_bits(bits)
            candidate |= (1 << (bits - 1))  # Ensure correct bit length
            candidate |= 1  # Ensure odd
            
            if self._miller_rabin(candidate):
                return candidate
            
            # Optional: log attempts for monitoring
            if attempts % 100 == 0:
                print(f"  ... tested {attempts} candidates")
    
    def generate_safe_prime(self, bits: int) -> int:
        """
        Generate a safe prime p where (p-1)/2 is also prime.
        
        Safe primes are used in Diffie-Hellman and other protocols.
        """
        while True:
            # Generate prime q
            q = self.generate_prime(bits - 1)
            
            # Check if p = 2q + 1 is also prime
            p = 2 * q + 1
            if self._miller_rabin(p):
                return p
    
    def generate_strong_prime(self, bits: int) -> int:
        """
        Generate a strong prime for RSA.
        
        Strong prime p satisfies:
        1. p - 1 has a large prime factor r
        2. p + 1 has a large prime factor s
        3. r - 1 has a large prime factor t
        """
        # Simplified: ensure (p-1)/2 has large prime factor
        while True:
            # Generate large prime factor
            r = self.generate_prime(bits - 64)
            
            # Find p such that p - 1 = 2 * k * r for small k
            for k in range(1, 1000):
                p = 2 * k * r + 1
                if p.bit_length() == bits and self._miller_rabin(p):
                    return p


# Usage example
generator = SecurePrimeGenerator(security_level=128)

# Generate RSA primes
print("Generating 512-bit prime...")
p = generator.generate_prime(512)
print(f"Prime: {p}")
print(f"Bit length: {p.bit_length()}")

# Verify
print(f"Verification: {miller_rabin_deterministic(p) if p.bit_length() <= 64 else 'Too large for deterministic test'}")
```

### 9.3 Integration with Cryptographic Systems

```python
from dataclasses import dataclass
from typing import Tuple


@dataclass
class RSAPublicKey:
    n: int
    e: int


@dataclass  
class RSAPrivateKey:
    n: int
    d: int
    p: int
    q: int


class RSAKeyPairGenerator:
    """
    RSA key pair generator using Miller-Rabin primality testing.
    """
    
    def __init__(self, key_bits: int = 2048):
        self.key_bits = key_bits
        self.prime_generator = SecurePrimeGenerator(security_level=128)
    
    def _extended_gcd(self, a: int, b: int) -> Tuple[int, int, int]:
        """Extended Euclidean Algorithm."""
        if a == 0:
            return b, 0, 1
        gcd, x1, y1 = self._extended_gcd(b % a, a)
        return gcd, y1 - (b // a) * x1, x1
    
    def _mod_inverse(self, a: int, m: int) -> int:
        """Compute modular inverse using Extended GCD."""
        gcd, x, _ = self._extended_gcd(a % m, m)
        if gcd != 1:
            raise ValueError("Modular inverse doesn't exist")
        return (x % m + m) % m
    
    def generate(self) -> Tuple[RSAPublicKey, RSAPrivateKey]:
        """
        Generate RSA key pair.
        
        Returns (public_key, private_key)
        """
        prime_bits = self.key_bits // 2
        
        # Generate two distinct primes
        p = self.prime_generator.generate_prime(prime_bits)
        q = self.prime_generator.generate_prime(prime_bits)
        
        while p == q:
            q = self.prime_generator.generate_prime(prime_bits)
        
        # Compute modulus and totient
        n = p * q
        phi_n = (p - 1) * (q - 1)
        
        # Public exponent (commonly 65537)
        e = 65537
        
        # Private exponent
        d = self._mod_inverse(e, phi_n)
        
        public_key = RSAPublicKey(n=n, e=e)
        private_key = RSAPrivateKey(n=n, d=d, p=p, q=q)
        
        return public_key, private_key


# Generate key pair
keygen = RSAKeyPairGenerator(key_bits=1024)  # Small for demo
public, private = keygen.generate()

print(f"Public key (e, n): ({public.e}, {public.n})")
print(f"Key size: {public.n.bit_length()} bits")
```

## 10. Comparison with Alternatives

| Algorithm | Type | Time | Error |
|-----------|------|------|-------|
| **Miller-Rabin** | Probabilistic | O(k log³ n) | ≤ 4⁻ᵏ |
| **Fermat** | Probabilistic | O(k log² n) | High (Carmichaels) |
| **Solovay-Strassen** | Probabilistic | O(k log³ n) | ≤ 2⁻ᵏ |
| **AKS** | Deterministic | O(log⁶ n) | 0 |
| **ECPP** | Deterministic | O(log⁵ n) expected | 0 |

## 11. Edge Cases

| Input | Output | Notes |
|-------|--------|-------|
| n < 2 | False | Not prime by definition |
| n = 2 | True | Only even prime |
| Even n > 2 | False | Divisible by 2 |
| Carmichael numbers | False | Miller-Rabin catches these |
| Perfect squares | False | Detected properly |

## 12. References

- Miller, G.L. (1976). "Riemann's Hypothesis and Tests for Primality"
- Rabin, M.O. (1980). "Probabilistic Algorithm for Testing Primality"
- [Wikipedia: Miller-Rabin](https://en.wikipedia.org/wiki/Miller%E2%80%93Rabin_primality_test)
- Menezes, Oorschot, Vanstone. "Handbook of Applied Cryptography"
