# Sieve of Eratosthenes

## Overview
- **Category**: Number Theory / Prime Numbers
- **Complexity**: Time: O(n log log n) | Space: O(n)
- **Type**: Prime number generation
- **Source File**: [maths/sieve_of_eratosthenes.py](../../../maths/sieve_of_eratosthenes.py)

## 1. Mathematical Foundation

### 1.1 Problem Definition

Find all prime numbers up to a given limit $n$.

A prime number $p > 1$ has exactly two divisors: 1 and itself.

### 1.2 Key Insight

If a composite number $m \leq n$ exists, it must have a prime factor $p \leq \sqrt{n}$.

**Proof:**
If $m = a \times b$ where $a, b > 1$, then either $a \leq \sqrt{m}$ or $b \leq \sqrt{m}$.
Therefore, to mark all composites up to $n$, we only need primes up to $\sqrt{n}$.

### 1.3 Sieving Formula

Mark all multiples of prime $p$ starting from $p^2$:

$$
\text{composite}[k] = \text{true}, \quad \text{for } k = p^2, p^2 + p, p^2 + 2p, \ldots \leq n
$$

Why start at $p^2$? Because smaller multiples $2p, 3p, \ldots, (p-1)p$ were already marked by smaller primes.

## 2. Algorithm Description

### 2.1 Intuition

1. Create a list of all numbers from 2 to n
2. Start with the first prime (2)
3. Mark all multiples of this prime as composite
4. Move to the next unmarked number (next prime)
5. Repeat until reaching √n

### 2.2 Optimization Insights

1. **Start at p²**: Skip already-marked multiples
2. **Only odd numbers**: 2 is the only even prime
3. **Wheel factorization**: Skip multiples of 2, 3, 5...
4. **Segmented sieve**: Process in blocks for cache efficiency

## 3. Pseudocode

```
ALGORITHM Sieve-of-Eratosthenes(n)
    INPUT: Upper limit n
    OUTPUT: List of all primes ≤ n
    
    // Initialize
    is_prime ← array of n+1 booleans, all true
    is_prime[0] ← false
    is_prime[1] ← false
    
    // Sieve
    for p ← 2 to √n do
        if is_prime[p] then
            // Mark multiples of p starting from p²
            for multiple ← p² to n step p do
                is_prime[multiple] ← false
    
    // Collect primes
    primes ← empty list
    for i ← 2 to n do
        if is_prime[i] then
            primes.append(i)
    
    return primes

ALGORITHM Optimized-Sieve(n)
    INPUT: Upper limit n
    OUTPUT: List of all primes ≤ n
    
    if n < 2 then
        return empty list
    
    // Only track odd numbers (index i represents 2i+1)
    size ← (n - 1) / 2
    is_prime ← array of size booleans, all true
    
    for i ← 1 to √n/2 do
        if is_prime[i] then
            p ← 2*i + 1  // Actual prime value
            // Start at p², stepping by 2p (skip even multiples)
            start ← (p*p - 1) / 2
            for j ← start to size step p do
                is_prime[j] ← false
    
    primes ← [2]
    for i ← 1 to size do
        if is_prime[i] then
            primes.append(2*i + 1)
    
    return primes
```

## 4. Complexity Analysis

### 4.1 Time Complexity

**O(n log log n)**

**Analysis:**
- For each prime $p$, we mark $\frac{n}{p}$ multiples
- Total operations: $\sum_{p \leq n, p \text{ prime}} \frac{n}{p} = n \sum_{p \leq n} \frac{1}{p}$
- By Mertens' theorem: $\sum_{p \leq n} \frac{1}{p} = \log \log n + O(1)$
- Therefore: $O(n \log \log n)$

### 4.2 Space Complexity

| Version | Space |
|---------|-------|
| Basic | O(n) bits |
| Odd-only | O(n/2) bits |
| Segmented | O(√n) |

### 4.3 Prime Counting Function

The number of primes up to $n$ is approximately:

$$
\pi(n) \approx \frac{n}{\ln n}
$$

More precisely (Prime Number Theorem):

$$
\pi(n) \sim \text{Li}(n) = \int_2^n \frac{dt}{\ln t}
$$

## 5. Visual Representation

### 5.1 Sieving Process for n = 30

```
Initial: 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30

Mark multiples of 2:
        2 3 ✗ 5 ✗ 7 ✗ 9 ✗  11 ✗  13 ✗  15 ✗  17 ✗  19 ✗  21 ✗  23 ✗  25 ✗  27 ✗  29 ✗

Mark multiples of 3 (from 9):
        2 3 ✗ 5 ✗ 7 ✗ ✗ ✗  11 ✗  13 ✗  ✗  ✗  17 ✗  19 ✗  ✗  ✗  23 ✗  25 ✗  ✗  ✗  29 ✗

Mark multiples of 5 (from 25):
        2 3 ✗ 5 ✗ 7 ✗ ✗ ✗  11 ✗  13 ✗  ✗  ✗  17 ✗  19 ✗  ✗  ✗  23 ✗  ✗  ✗  ✗  ✗  29 ✗

√30 ≈ 5.5, so we stop.

Primes: 2, 3, 5, 7, 11, 13, 17, 19, 23, 29
```

### 5.2 Grid Visualization

```
     1   2   3   4   5   6   7   8   9  10
   +---+---+---+---+---+---+---+---+---+---+
 1 |   | P | P |   | P |   | P |   |   |   |
   +---+---+---+---+---+---+---+---+---+---+
 2 | P |   | P |   |   |   | P |   | P |   |
   +---+---+---+---+---+---+---+---+---+---+
 3 |   |   | P |   |   |   |   |   | P |   |
   +---+---+---+---+---+---+---+---+---+---+

P = Prime, blank = composite
Row 1: 2,3,5,7
Row 2: 11,13,17,19
Row 3: 23,29
```

### 5.3 Process Diagram

```mermaid
flowchart TD
    A[Initialize all as prime] --> B[p = 2]
    B --> C{p² ≤ n?}
    C -->|Yes| D{is_prime[p]?}
    C -->|No| H[Collect primes]
    D -->|Yes| E[Mark p², p²+p, p²+2p, ...]
    D -->|No| G[p = p + 1]
    E --> G
    G --> C
    H --> I[Return prime list]
```

## 6. Implementation

```python
def sieve_basic(n: int) -> list[int]:
    """
    Basic Sieve of Eratosthenes.
    
    >>> sieve_basic(30)
    [2, 3, 5, 7, 11, 13, 17, 19, 23, 29]
    >>> sieve_basic(10)
    [2, 3, 5, 7]
    >>> sieve_basic(1)
    []
    """
    if n < 2:
        return []
    
    is_prime = [True] * (n + 1)
    is_prime[0] = is_prime[1] = False
    
    for p in range(2, int(n**0.5) + 1):
        if is_prime[p]:
            for multiple in range(p*p, n + 1, p):
                is_prime[multiple] = False
    
    return [i for i, prime in enumerate(is_prime) if prime]


def sieve_optimized(n: int) -> list[int]:
    """
    Optimized sieve using odd numbers only.
    
    >>> sieve_optimized(30)
    [2, 3, 5, 7, 11, 13, 17, 19, 23, 29]
    """
    if n < 2:
        return []
    if n == 2:
        return [2]
    
    # Only track odd numbers
    size = (n - 1) // 2
    is_prime = [True] * (size + 1)
    
    for i in range(1, int(n**0.5) // 2 + 1):
        if is_prime[i]:
            p = 2 * i + 1
            start = (p * p) // 2
            for j in range(start, size + 1, p):
                is_prime[j] = False
    
    primes = [2]
    primes.extend(2*i + 1 for i in range(1, size + 1) if is_prime[i])
    return primes


def segmented_sieve(n: int) -> list[int]:
    """
    Segmented sieve for better cache performance.
    
    >>> segmented_sieve(100)[:10]
    [2, 3, 5, 7, 11, 13, 17, 19, 23, 29]
    """
    if n < 2:
        return []
    
    limit = int(n**0.5) + 1
    
    # Get small primes up to √n
    small_primes = sieve_basic(limit)
    
    # Segment size (L1 cache friendly)
    segment_size = max(limit, 32768)
    
    primes = list(small_primes)
    
    # Process segments
    low = limit + 1
    while low <= n:
        high = min(low + segment_size - 1, n)
        
        # Initialize segment
        is_prime = [True] * (high - low + 1)
        
        # Mark composites in this segment
        for p in small_primes:
            start = ((low + p - 1) // p) * p
            if start < p * p:
                start = p * p
            
            for j in range(start, high + 1, p):
                is_prime[j - low] = False
        
        # Collect primes from segment
        for i in range(high - low + 1):
            if is_prime[i]:
                primes.append(low + i)
        
        low = high + 1
    
    return primes


def count_primes(n: int) -> int:
    """
    Count primes up to n using sieve.
    
    >>> count_primes(100)
    25
    >>> count_primes(1000000)
    78498
    """
    if n < 2:
        return 0
    
    is_prime = [True] * (n + 1)
    is_prime[0] = is_prime[1] = False
    
    for p in range(2, int(n**0.5) + 1):
        if is_prime[p]:
            for multiple in range(p*p, n + 1, p):
                is_prime[multiple] = False
    
    return sum(is_prime)
```

## 7. Variations

### 7.1 Sieve of Sundaram

Generates odd primes only:

```python
def sieve_sundaram(n: int) -> list[int]:
    """
    Sieve of Sundaram for odd primes.
    
    >>> sieve_sundaram(30)
    [2, 3, 5, 7, 11, 13, 17, 19, 23, 29]
    """
    if n < 2:
        return []
    if n == 2:
        return [2]
    
    k = (n - 1) // 2
    integers = [True] * (k + 1)
    
    for i in range(1, k + 1):
        j = i
        while i + j + 2*i*j <= k:
            integers[i + j + 2*i*j] = False
            j += 1
    
    primes = [2]
    primes.extend(2*i + 1 for i in range(1, k + 1) if integers[i])
    return primes
```

### 7.2 Euler's Sieve (Linear Sieve)

Each composite is marked exactly once:

```python
def linear_sieve(n: int) -> list[int]:
    """
    Euler's linear sieve - O(n) time.
    
    >>> linear_sieve(30)
    [2, 3, 5, 7, 11, 13, 17, 19, 23, 29]
    """
    if n < 2:
        return []
    
    smallest_prime = [0] * (n + 1)
    primes = []
    
    for i in range(2, n + 1):
        if smallest_prime[i] == 0:
            smallest_prime[i] = i
            primes.append(i)
        
        for p in primes:
            if p > smallest_prime[i] or i * p > n:
                break
            smallest_prime[i * p] = p
    
    return primes
```

## 8. Real-World Software Engineering Applications

### 8.1 Industry Use Cases

1. **Cryptography**
   - RSA key generation needs large primes
   - Diffie-Hellman parameter generation
   - Primality testing preprocessing

2. **Hash Table Design**
   - Prime bucket sizes reduce collisions
   - Double hashing with prime step sizes

3. **Random Number Generation**
   - Linear congruential generators use prime moduli
   - Mersenne primes for Mersenne Twister

4. **Competitive Programming**
   - Number theory problems
   - Prime factorization queries

5. **Mathematical Software**
   - Computer algebra systems
   - Number theory research

### 8.2 Production Example: Prime-Based Hash Table

```python
class PrimeHashTable:
    """
    Hash table using prime bucket sizes for better distribution.
    """
    
    # Precomputed primes for common sizes
    PRIME_SIZES = [
        53, 97, 193, 389, 769, 1543, 3079, 6151, 12289, 
        24593, 49157, 98317, 196613, 393241, 786433
    ]
    
    def __init__(self, initial_size: int = 53):
        self.size_index = 0
        while (self.size_index < len(self.PRIME_SIZES) - 1 and 
               self.PRIME_SIZES[self.size_index] < initial_size):
            self.size_index += 1
        
        self.size = self.PRIME_SIZES[self.size_index]
        self.buckets = [[] for _ in range(self.size)]
        self.count = 0
    
    def _hash(self, key: str) -> int:
        """Simple hash function."""
        h = 0
        for c in key:
            h = (h * 31 + ord(c)) % self.size
        return h
    
    def put(self, key: str, value: any) -> None:
        """Insert or update key-value pair."""
        if self.count >= self.size * 0.7:
            self._resize()
        
        index = self._hash(key)
        bucket = self.buckets[index]
        
        for i, (k, v) in enumerate(bucket):
            if k == key:
                bucket[i] = (key, value)
                return
        
        bucket.append((key, value))
        self.count += 1
    
    def get(self, key: str) -> any:
        """Retrieve value by key."""
        index = self._hash(key)
        for k, v in self.buckets[index]:
            if k == key:
                return v
        raise KeyError(key)
    
    def _resize(self) -> None:
        """Resize to next prime size."""
        if self.size_index >= len(self.PRIME_SIZES) - 1:
            return
        
        old_buckets = self.buckets
        self.size_index += 1
        self.size = self.PRIME_SIZES[self.size_index]
        self.buckets = [[] for _ in range(self.size)]
        self.count = 0
        
        for bucket in old_buckets:
            for key, value in bucket:
                self.put(key, value)


# Prime generator for arbitrary sizes
def next_prime(n: int) -> int:
    """Find the smallest prime >= n."""
    if n <= 2:
        return 2
    if n % 2 == 0:
        n += 1
    
    while True:
        if is_prime(n):
            return n
        n += 2

def is_prime(n: int) -> bool:
    """Simple primality test."""
    if n < 2:
        return False
    if n == 2:
        return True
    if n % 2 == 0:
        return False
    for i in range(3, int(n**0.5) + 1, 2):
        if n % i == 0:
            return False
    return True
```

### 8.3 Prime Factorization with Sieve

```python
class PrimeFactorizer:
    """
    Fast prime factorization using sieve preprocessing.
    """
    
    def __init__(self, max_n: int):
        self.max_n = max_n
        self.smallest_prime = [0] * (max_n + 1)
        self._build_sieve()
    
    def _build_sieve(self) -> None:
        """Build smallest prime factor sieve."""
        for i in range(2, self.max_n + 1):
            if self.smallest_prime[i] == 0:
                for j in range(i, self.max_n + 1, i):
                    if self.smallest_prime[j] == 0:
                        self.smallest_prime[j] = i
    
    def factorize(self, n: int) -> dict[int, int]:
        """
        Return prime factorization as {prime: exponent}.
        
        >>> pf = PrimeFactorizer(1000)
        >>> pf.factorize(360)
        {2: 3, 3: 2, 5: 1}
        >>> pf.factorize(17)
        {17: 1}
        """
        if n > self.max_n:
            raise ValueError(f"n must be <= {self.max_n}")
        
        factors = {}
        while n > 1:
            p = self.smallest_prime[n]
            count = 0
            while n % p == 0:
                n //= p
                count += 1
            factors[p] = count
        
        return factors
    
    def count_divisors(self, n: int) -> int:
        """
        Count number of divisors.
        
        >>> pf = PrimeFactorizer(1000)
        >>> pf.count_divisors(360)  # 360 = 2³ × 3² × 5¹
        24
        """
        factors = self.factorize(n)
        result = 1
        for exp in factors.values():
            result *= (exp + 1)
        return result
```

## 9. Comparison of Sieve Variants

| Algorithm | Time | Space | Best For |
|-----------|------|-------|----------|
| Basic Eratosthenes | O(n log log n) | O(n) | Small n, simplicity |
| Odd-only | O(n log log n) | O(n/2) | 2x memory savings |
| Segmented | O(n log log n) | O(√n) | Large n, cache efficiency |
| Linear (Euler) | O(n) | O(n) | Need smallest prime factor |
| Sundaram | O(n log n) | O(n/2) | Educational |

## 10. Benchmarks

Approximate times on modern hardware:

| n | Basic | Optimized | Segmented |
|---|-------|-----------|-----------|
| 10⁶ | 10 ms | 5 ms | 8 ms |
| 10⁷ | 120 ms | 60 ms | 70 ms |
| 10⁸ | 1.5 s | 0.7 s | 0.8 s |
| 10⁹ | 18 s | 9 s | 8 s |

## 11. Edge Cases

| Input | Output | Notes |
|-------|--------|-------|
| n < 2 | [] | No primes |
| n = 2 | [2] | Single prime |
| n = 3 | [2, 3] | Two primes |

## 12. References

- [Wikipedia: Sieve of Eratosthenes](https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes)
- [CP-Algorithms: Sieve](https://cp-algorithms.com/algebra/sieve-of-eratosthenes.html)
- Pritchard, P. (1987). "Linear prime-number sieves: a family tree"
