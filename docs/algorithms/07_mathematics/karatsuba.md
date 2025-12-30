# Karatsuba Multiplication

## Overview
- **Category**: Divide and Conquer / Arithmetic
- **Complexity**: Time: O(n^1.585) | Space: O(n)
- **Type**: Fast multiplication algorithm
- **Source File**: [maths/karatsuba.py](../../../maths/karatsuba.py)

## 1. Mathematical Foundation

### 1.1 Problem Definition

Multiply two n-digit numbers faster than the naive O(n²) algorithm.

### 1.2 Key Insight

For numbers $x$ and $y$ with $n$ digits each, split into high and low halves:

$$
x = x_H \cdot B^{n/2} + x_L, \quad y = y_H \cdot B^{n/2} + y_L
$$

where $B$ is the base (typically 10 or 2^32).

### 1.3 Naive Expansion

$$
x \cdot y = x_H y_H \cdot B^n + (x_H y_L + x_L y_H) \cdot B^{n/2} + x_L y_L
$$

This requires **4 multiplications** of half-size numbers.

### 1.4 Karatsuba's Trick

Compute only **3 multiplications**:
- $z_0 = x_L \cdot y_L$
- $z_2 = x_H \cdot y_H$
- $z_1 = (x_L + x_H)(y_L + y_H) - z_0 - z_2$

Then:
$$
x \cdot y = z_2 \cdot B^n + z_1 \cdot B^{n/2} + z_0
$$

**Why $z_1$ works:**
$$
(x_L + x_H)(y_L + y_H) = x_L y_L + x_L y_H + x_H y_L + x_H y_H = z_0 + (x_H y_L + x_L y_H) + z_2
$$

### 1.5 Complexity Analysis

Recurrence: $T(n) = 3T(n/2) + O(n)$

By Master Theorem: $T(n) = O(n^{\log_2 3}) = O(n^{1.585})$

## 2. Algorithm Description

### 2.1 Intuition

Trade one multiplication for additions/subtractions:
- 4 multiplications → 3 multiplications + 4 additions
- Recursively apply to sub-problems
- Base case: single-digit multiplication

### 2.2 Comparison

| Algorithm | Multiplications | Complexity |
|-----------|-----------------|------------|
| Naive (grade school) | n² | O(n²) |
| Karatsuba | 3·3·3·... | O(n^1.585) |
| Toom-Cook 3-way | 5 of n/3 | O(n^1.465) |
| Schönhage-Strassen | FFT-based | O(n log n log log n) |

## 3. Pseudocode

```
ALGORITHM Karatsuba(x, y)
    INPUT: Two n-digit integers x and y
    OUTPUT: Product x × y
    
    // Base case
    if n ≤ THRESHOLD then
        return x × y  // Use naive multiplication
    
    // Split numbers
    m ← ⌈n/2⌉
    B ← 10^m  // or 2^(32m) for binary
    
    x_H ← x div B
    x_L ← x mod B
    y_H ← y div B
    y_L ← y mod B
    
    // Three recursive multiplications
    z_0 ← Karatsuba(x_L, y_L)
    z_2 ← Karatsuba(x_H, y_H)
    z_1 ← Karatsuba(x_L + x_H, y_L + y_H) - z_0 - z_2
    
    // Combine results
    return z_2 × B² + z_1 × B + z_0
```

## 4. Step-by-Step Example

### Example: Multiply 1234 × 5678

**Split**: $n = 4$, $m = 2$, $B = 100$
- $x = 1234 = 12 \cdot 100 + 34$ → $x_H = 12$, $x_L = 34$
- $y = 5678 = 56 \cdot 100 + 78$ → $y_H = 56$, $y_L = 78$

**Compute products**:
- $z_0 = 34 \times 78 = 2652$
- $z_2 = 12 \times 56 = 672$
- $z_1 = (34 + 12)(78 + 56) - 2652 - 672 = 46 \times 134 - 3324 = 6164 - 3324 = 2840$

**Combine**:
$$
1234 \times 5678 = 672 \times 10000 + 2840 \times 100 + 2652 = 7004652
$$

**Verification**: $1234 \times 5678 = 7,006,652$ ✓

(Note: There's a calculation error in my example - let me recalculate)

$46 \times 134 = 6164$
$6164 - 2652 - 672 = 2840$
$672 \times 10000 + 2840 \times 100 + 2652 = 6720000 + 284000 + 2652 = 7006652$ ✓

## 5. Complexity Analysis

### 5.1 Time Complexity

**Recurrence**: $T(n) = 3T(n/2) + \Theta(n)$

Using Master Theorem ($a = 3$, $b = 2$, $f(n) = n$):
- $\log_b a = \log_2 3 \approx 1.585$
- $n^{\log_b a} = n^{1.585}$ dominates $f(n) = n$

**Result**: $T(n) = \Theta(n^{\log_2 3}) = \Theta(n^{1.585})$

### 5.2 Practical Crossover

Karatsuba has higher constant factors due to:
- Additions/subtractions overhead
- Function call overhead
- Memory allocation

**Typical crossover**: n ≈ 20-100 digits

### 5.3 Space Complexity

$O(n)$ for intermediate results
$O(\log n)$ recursion depth

## 6. Visual Representation

### 6.1 Divide and Conquer Tree

```
                    x × y
                   /  |  \
                  /   |   \
            z₀=xL×yL  |   z₂=xH×yH
                      |
              z₁=(xL+xH)×(yL+yH)-z₀-z₂
             /         |         \
            /          |          \
          ...         ...         ...
          
3 branches at each level → 3^(log₂ n) = n^1.585 leaf operations
```

### 6.2 Comparison: 4 vs 3 Multiplications

```
Naive:          Karatsuba:
    ×             ×
   /|\           /|\
  / | \         / | \
xHyH   xLyL   z₂  z₁  z₀
  xHyL             ↑
  xLyH        computed from
              (xL+xH)(yL+yH)
              
4 mults         3 mults
```

## 7. Implementation

```python
from typing import Union


def karatsuba(x: int, y: int) -> int:
    """
    Karatsuba multiplication algorithm.
    
    Time: O(n^1.585) where n is number of digits
    
    >>> karatsuba(1234, 5678)
    7006652
    >>> karatsuba(12345678, 87654321)
    1082152022374638
    >>> karatsuba(0, 12345)
    0
    >>> karatsuba(1, 999)
    999
    """
    # Handle signs
    sign = 1
    if x < 0:
        x, sign = -x, -sign
    if y < 0:
        y, sign = -y, -sign
    
    # Base case: small numbers
    if x < 10 or y < 10:
        return sign * x * y
    
    # Determine size
    n = max(len(str(x)), len(str(y)))
    m = n // 2
    
    # Split numbers
    base = 10 ** m
    x_high, x_low = divmod(x, base)
    y_high, y_low = divmod(y, base)
    
    # Recursive multiplications (only 3!)
    z0 = karatsuba(x_low, y_low)
    z2 = karatsuba(x_high, y_high)
    z1 = karatsuba(x_low + x_high, y_low + y_high) - z0 - z2
    
    # Combine
    result = z2 * (base ** 2) + z1 * base + z0
    
    return sign * result


def karatsuba_binary(x: int, y: int) -> int:
    """
    Karatsuba for binary representation (more efficient for computers).
    
    >>> karatsuba_binary(1234, 5678)
    7006652
    """
    sign = 1
    if x < 0:
        x, sign = -x, -sign
    if y < 0:
        y, sign = -y, -sign
    
    # Base case
    if x == 0 or y == 0:
        return 0
    if x < (1 << 64) and y < (1 << 64):
        return sign * x * y  # Use native multiplication
    
    # Find split point (half of larger number's bits)
    n = max(x.bit_length(), y.bit_length())
    m = (n + 1) // 2
    
    # Split using bit operations
    mask = (1 << m) - 1
    x_low = x & mask
    x_high = x >> m
    y_low = y & mask
    y_high = y >> m
    
    # Three recursive multiplications
    z0 = karatsuba_binary(x_low, y_low)
    z2 = karatsuba_binary(x_high, y_high)
    z1 = karatsuba_binary(x_low + x_high, y_low + y_high) - z0 - z2
    
    # Combine using shifts
    result = (z2 << (2 * m)) + (z1 << m) + z0
    
    return sign * result


def karatsuba_with_threshold(x: int, y: int, threshold: int = 64) -> int:
    """
    Karatsuba with configurable threshold for base case.
    
    Uses naive multiplication below threshold for better performance.
    
    >>> karatsuba_with_threshold(12345678901234567890, 98765432109876543210)
    1219326311370217952237463801111263526900
    """
    sign = 1
    if x < 0:
        x, sign = -x, -sign
    if y < 0:
        y, sign = -y, -sign
    
    # Use native multiplication for small numbers
    if x.bit_length() <= threshold or y.bit_length() <= threshold:
        return sign * x * y
    
    n = max(x.bit_length(), y.bit_length())
    m = n // 2
    
    mask = (1 << m) - 1
    x_low, x_high = x & mask, x >> m
    y_low, y_high = y & mask, y >> m
    
    z0 = karatsuba_with_threshold(x_low, y_low, threshold)
    z2 = karatsuba_with_threshold(x_high, y_high, threshold)
    z1 = karatsuba_with_threshold(x_low + x_high, y_low + y_high, threshold) - z0 - z2
    
    return sign * ((z2 << (2 * m)) + (z1 << m) + z0)


class BigInteger:
    """
    Big integer class using Karatsuba multiplication.
    """
    
    def __init__(self, value: Union[int, str] = 0):
        if isinstance(value, str):
            self.value = int(value)
        else:
            self.value = value
    
    def __mul__(self, other: 'BigInteger') -> 'BigInteger':
        """Multiply using Karatsuba."""
        return BigInteger(karatsuba_binary(self.value, other.value))
    
    def __add__(self, other: 'BigInteger') -> 'BigInteger':
        return BigInteger(self.value + other.value)
    
    def __sub__(self, other: 'BigInteger') -> 'BigInteger':
        return BigInteger(self.value - other.value)
    
    def __repr__(self) -> str:
        return f"BigInteger({self.value})"
    
    def __eq__(self, other: 'BigInteger') -> bool:
        return self.value == other.value


def multiply_polynomials_karatsuba(p: list, q: list) -> list:
    """
    Multiply two polynomials using Karatsuba-style algorithm.
    
    Polynomials represented as coefficient lists: p[i] is coefficient of x^i.
    
    >>> multiply_polynomials_karatsuba([1, 2], [3, 4])  # (1+2x)(3+4x) = 3+10x+8x²
    [3, 10, 8]
    >>> multiply_polynomials_karatsuba([1, 2, 1], [1, 1])  # (1+2x+x²)(1+x)
    [1, 3, 3, 1]
    """
    # Pad to same length
    n = max(len(p), len(q))
    p = p + [0] * (n - len(p))
    q = q + [0] * (n - len(q))
    
    return _poly_karatsuba(p, q)


def _poly_karatsuba(p: list, q: list) -> list:
    """Recursive polynomial Karatsuba."""
    n = len(p)
    
    # Base case
    if n <= 4:
        result = [0] * (2 * n - 1)
        for i in range(n):
            for j in range(n):
                result[i + j] += p[i] * q[j]
        return result
    
    # Split
    m = n // 2
    p_low, p_high = p[:m], p[m:]
    q_low, q_high = q[:m], q[m:]
    
    # Pad if necessary
    while len(p_high) < len(p_low):
        p_high.append(0)
    while len(q_high) < len(q_low):
        q_high.append(0)
    
    # Three recursive multiplications
    z0 = _poly_karatsuba(p_low, q_low)
    z2 = _poly_karatsuba(p_high, q_high)
    
    # p_low + p_high, q_low + q_high
    p_sum = [p_low[i] + p_high[i] for i in range(len(p_low))]
    q_sum = [q_low[i] + q_high[i] for i in range(len(q_low))]
    z1_temp = _poly_karatsuba(p_sum, q_sum)
    
    # z1 = z1_temp - z0 - z2
    z1 = z1_temp[:]
    for i in range(len(z0)):
        z1[i] -= z0[i]
    for i in range(len(z2)):
        z1[i] -= z2[i]
    
    # Combine: z0 + z1*x^m + z2*x^(2m)
    result_len = 2 * n - 1
    result = [0] * result_len
    
    for i, v in enumerate(z0):
        result[i] += v
    for i, v in enumerate(z1):
        if i + m < result_len:
            result[i + m] += v
    for i, v in enumerate(z2):
        if i + 2 * m < result_len:
            result[i + 2 * m] += v
    
    return result
```

## 8. Applications

### 8.1 Arbitrary-Precision Arithmetic

Libraries like GMP use Karatsuba for medium-sized numbers.

### 8.2 Polynomial Multiplication

Same algorithm applies to polynomial coefficients.

### 8.3 Cryptographic Operations

RSA, ECC require multiplication of 2048+ bit numbers.

## 9. Real-World Software Engineering Applications

### 9.1 Industry Use Cases

1. **Cryptographic Libraries**
   - OpenSSL, BoringSSL
   - Python's built-in `int` type

2. **Computer Algebra Systems**
   - Mathematica, Maple
   - SymPy

3. **Scientific Computing**
   - Arbitrary precision physics simulations

4. **Blockchain**
   - Large number arithmetic for signatures

### 9.2 Production Example: Optimized Big Integer Library

```python
import time
from typing import List, Tuple


class OptimizedBigInt:
    """
    Optimized big integer with Karatsuba multiplication.
    
    Uses base 2^30 for efficient operations.
    """
    
    BASE = 1 << 30  # 2^30
    BASE_BITS = 30
    KARATSUBA_THRESHOLD = 50  # Switch to naive below this many limbs
    
    def __init__(self, value: int = 0):
        self.negative = value < 0
        value = abs(value)
        
        # Convert to limbs (base 2^30 representation)
        self.limbs: List[int] = []
        while value > 0:
            self.limbs.append(value % self.BASE)
            value //= self.BASE
        
        if not self.limbs:
            self.limbs = [0]
    
    def to_int(self) -> int:
        """Convert back to Python int."""
        result = 0
        for i, limb in enumerate(self.limbs):
            result += limb * (self.BASE ** i)
        return -result if self.negative else result
    
    @staticmethod
    def _naive_multiply(a: List[int], b: List[int]) -> List[int]:
        """O(n²) multiplication for small numbers."""
        if not a or not b:
            return [0]
        
        result = [0] * (len(a) + len(b))
        
        for i, ai in enumerate(a):
            carry = 0
            for j, bj in enumerate(b):
                temp = result[i + j] + ai * bj + carry
                result[i + j] = temp % OptimizedBigInt.BASE
                carry = temp // OptimizedBigInt.BASE
            
            if carry:
                result[i + len(b)] += carry
        
        # Remove leading zeros
        while len(result) > 1 and result[-1] == 0:
            result.pop()
        
        return result
    
    @staticmethod
    def _add_limbs(a: List[int], b: List[int]) -> List[int]:
        """Add two limb arrays."""
        n = max(len(a), len(b))
        result = [0] * (n + 1)
        carry = 0
        
        for i in range(n):
            ai = a[i] if i < len(a) else 0
            bi = b[i] if i < len(b) else 0
            temp = ai + bi + carry
            result[i] = temp % OptimizedBigInt.BASE
            carry = temp // OptimizedBigInt.BASE
        
        result[n] = carry
        
        while len(result) > 1 and result[-1] == 0:
            result.pop()
        
        return result
    
    @staticmethod
    def _sub_limbs(a: List[int], b: List[int]) -> List[int]:
        """Subtract b from a (assumes a >= b)."""
        result = a[:]
        borrow = 0
        
        for i in range(len(b)):
            temp = result[i] - b[i] - borrow
            if temp < 0:
                temp += OptimizedBigInt.BASE
                borrow = 1
            else:
                borrow = 0
            result[i] = temp
        
        for i in range(len(b), len(result)):
            temp = result[i] - borrow
            if temp < 0:
                temp += OptimizedBigInt.BASE
                borrow = 1
            else:
                borrow = 0
            result[i] = temp
        
        while len(result) > 1 and result[-1] == 0:
            result.pop()
        
        return result
    
    @staticmethod
    def _karatsuba_limbs(a: List[int], b: List[int]) -> List[int]:
        """Karatsuba multiplication on limb arrays."""
        n = max(len(a), len(b))
        
        # Base case: use naive for small numbers
        if n < OptimizedBigInt.KARATSUBA_THRESHOLD:
            return OptimizedBigInt._naive_multiply(a, b)
        
        # Pad to same length
        a = a + [0] * (n - len(a))
        b = b + [0] * (n - len(b))
        
        m = n // 2
        
        # Split
        a_low, a_high = a[:m], a[m:]
        b_low, b_high = b[:m], b[m:]
        
        # Three recursive multiplications
        z0 = OptimizedBigInt._karatsuba_limbs(a_low, b_low)
        z2 = OptimizedBigInt._karatsuba_limbs(a_high, b_high)
        
        a_sum = OptimizedBigInt._add_limbs(a_low, a_high)
        b_sum = OptimizedBigInt._add_limbs(b_low, b_high)
        z1_full = OptimizedBigInt._karatsuba_limbs(a_sum, b_sum)
        
        # z1 = z1_full - z0 - z2
        z1 = OptimizedBigInt._sub_limbs(z1_full, z0)
        z1 = OptimizedBigInt._sub_limbs(z1, z2)
        
        # Combine: z0 + z1*B^m + z2*B^(2m)
        result = z0 + [0] * (2 * n - len(z0))
        
        # Add z1 * B^m
        for i, v in enumerate(z1):
            result[i + m] += v
        
        # Add z2 * B^(2m)
        for i, v in enumerate(z2):
            result[i + 2 * m] += v
        
        # Propagate carries
        carry = 0
        for i in range(len(result)):
            result[i] += carry
            carry = result[i] // OptimizedBigInt.BASE
            result[i] %= OptimizedBigInt.BASE
        
        while carry:
            result.append(carry % OptimizedBigInt.BASE)
            carry //= OptimizedBigInt.BASE
        
        while len(result) > 1 and result[-1] == 0:
            result.pop()
        
        return result
    
    def __mul__(self, other: 'OptimizedBigInt') -> 'OptimizedBigInt':
        """Multiply using Karatsuba."""
        result = OptimizedBigInt()
        result.limbs = self._karatsuba_limbs(self.limbs, other.limbs)
        result.negative = self.negative != other.negative
        return result
    
    def __repr__(self) -> str:
        return f"OptimizedBigInt({self.to_int()})"


def benchmark_multiplication(bits: int, iterations: int = 10) -> dict:
    """Compare Karatsuba vs Python native multiplication."""
    import random
    
    # Generate random numbers
    a = random.getrandbits(bits)
    b = random.getrandbits(bits)
    
    # Native Python
    start = time.time()
    for _ in range(iterations):
        _ = a * b
    native_time = time.time() - start
    
    # Karatsuba
    start = time.time()
    for _ in range(iterations):
        _ = karatsuba_binary(a, b)
    karatsuba_time = time.time() - start
    
    return {
        'bits': bits,
        'native_time': native_time,
        'karatsuba_time': karatsuba_time,
        'ratio': karatsuba_time / native_time
    }


# Note: Python's built-in int already uses Karatsuba internally,
# so our implementation will be slower. This is for educational purposes.
print("Benchmark (Python native vs pure Python Karatsuba):")
for bits in [128, 256, 512, 1024]:
    results = benchmark_multiplication(bits, 100)
    print(f"{bits} bits: native={results['native_time']:.4f}s, "
          f"karatsuba={results['karatsuba_time']:.4f}s, "
          f"ratio={results['ratio']:.2f}x")
```

## 10. Comparison with Other Algorithms

| Algorithm | Complexity | Practical Range |
|-----------|------------|-----------------|
| **Grade School** | O(n²) | n < 30 |
| **Karatsuba** | O(n^1.585) | 30 < n < 10000 |
| **Toom-Cook 3** | O(n^1.465) | 10000 < n < 100000 |
| **Schönhage-Strassen** | O(n log n log log n) | n > 100000 |
| **Harvey-Hoeven** | O(n log n) | Theoretical |

## 11. Edge Cases

| Input | Output | Notes |
|-------|--------|-------|
| x = 0 or y = 0 | 0 | Early termination |
| Single digit | Direct multiply | Base case |
| Different sizes | Pad shorter | Handle asymmetric |
| Negative numbers | Track sign | Handle in wrapper |

## 12. References

- Karatsuba, A. (1962). "Multiplication of Many-Digital Numbers by Automatic Computers"
- [Wikipedia: Karatsuba Algorithm](https://en.wikipedia.org/wiki/Karatsuba_algorithm)
- Knuth, D.E. "The Art of Computer Programming, Volume 2"
- GMP Library documentation
