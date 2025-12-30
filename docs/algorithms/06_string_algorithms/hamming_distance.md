# Hamming Distance

## Overview
- **Category**: String Metrics / Error Detection
- **Complexity**: Time: O(n) | Space: O(1)
- **Type**: Distance metric for equal-length strings
- **Source File**: [strings/hamming_distance.py](../../../strings/hamming_distance.py)

## 1. Mathematical Foundation

### 1.1 Problem Definition

The Hamming distance between two strings $s_1$ and $s_2$ of equal length $n$ is the number of positions at which the corresponding symbols differ.

$$
d_H(s_1, s_2) = |\{i : s_1[i] \neq s_2[i], 0 \leq i < n\}|
$$

### 1.2 Formal Properties

For strings of length $n$ over alphabet $\Sigma$:

1. **Non-negativity**: $d_H(s_1, s_2) \geq 0$
2. **Identity**: $d_H(s_1, s_2) = 0 \Leftrightarrow s_1 = s_2$
3. **Symmetry**: $d_H(s_1, s_2) = d_H(s_2, s_1)$
4. **Triangle inequality**: $d_H(s_1, s_3) \leq d_H(s_1, s_2) + d_H(s_2, s_3)$
5. **Bounded**: $0 \leq d_H(s_1, s_2) \leq n$

### 1.3 Relationship to XOR

For binary strings:
$$
d_H(s_1, s_2) = \text{popcount}(s_1 \oplus s_2)
$$

Where $\oplus$ is XOR and popcount counts the number of 1-bits.

## 2. Algorithm Description

### 2.1 Intuition

Simply iterate through both strings simultaneously and count mismatches.

### 2.2 For Binary Data

Use XOR operation followed by bit counting (Brian Kernighan's algorithm or hardware popcount).

## 3. Pseudocode

```
ALGORITHM Hamming-Distance(s1, s2)
    INPUT: Two strings s1, s2 of equal length n
    OUTPUT: Number of positions where characters differ
    
    if length(s1) ≠ length(s2) then
        raise "Strings must have equal length"
    
    distance ← 0
    for i ← 0 to n-1 do
        if s1[i] ≠ s2[i] then
            distance ← distance + 1
    
    return distance

ALGORITHM Hamming-Distance-Binary(x, y)
    INPUT: Two integers x, y
    OUTPUT: Number of bit positions where they differ
    
    xor_result ← x XOR y
    distance ← 0
    
    while xor_result ≠ 0 do
        distance ← distance + 1
        xor_result ← xor_result AND (xor_result - 1)  // Clear lowest set bit
    
    return distance
```

## 4. Complexity Analysis

### 4.1 Time Complexity

| Implementation | Complexity |
|---------------|------------|
| Character-by-character | O(n) |
| Binary (popcount) | O(number of differing bits) |
| Hardware popcount | O(1) for fixed-width integers |

### 4.2 Space Complexity
- **O(1)** - Only a counter variable needed

## 5. Visual Representation

### 5.1 String Example

```
Position: 0 1 2 3 4 5 6
String 1: k a r o l i n
String 2: k a t h r i n
          = = ≠ ≠ ≠ = =
          
Hamming Distance = 3 (positions 2, 3, 4 differ)
```

### 5.2 Binary Example

```
Binary 1: 1 0 1 1 1 0 1
Binary 2: 1 0 0 1 0 0 1
          = = ≠ = ≠ = =
XOR:      0 0 1 0 1 0 0

Hamming Distance = 2 (popcount of XOR result)
```

### 5.3 Hamming Cube Visualization

```
For 3-bit strings, Hamming distance forms edges of a cube:

        111
       / | \
     110 101 011
      |\ /|\ /|
      | X | X |
      |/ \|/ \|
     100 010 001
       \ | /
        000

Adjacent vertices have Hamming distance 1.
Opposite vertices (000-111) have distance 3.
```

## 6. Implementation

```python
def hamming_distance(s1: str, s2: str) -> int:
    """
    Calculate Hamming distance between two equal-length strings.
    
    >>> hamming_distance("karolin", "kathrin")
    3
    >>> hamming_distance("1011101", "1001001")
    2
    >>> hamming_distance("", "")
    0
    """
    if len(s1) != len(s2):
        raise ValueError("Strings must have equal length")
    
    return sum(c1 != c2 for c1, c2 in zip(s1, s2))


def hamming_distance_binary(x: int, y: int) -> int:
    """
    Calculate Hamming distance between two integers (binary representation).
    
    >>> hamming_distance_binary(1, 4)
    2
    >>> hamming_distance_binary(0b1011101, 0b1001001)
    2
    """
    xor = x ^ y
    distance = 0
    
    # Brian Kernighan's algorithm
    while xor:
        distance += 1
        xor &= xor - 1
    
    return distance


def hamming_distance_builtin(x: int, y: int) -> int:
    """Using Python's built-in bit_count (Python 3.10+)."""
    return (x ^ y).bit_count()
```

## 7. Applications in Coding Theory

### 7.1 Error Detection and Correction

The **minimum Hamming distance** of a code determines its error-detecting and error-correcting capabilities:

- Detect up to $d-1$ errors where $d$ is minimum distance
- Correct up to $\lfloor(d-1)/2\rfloor$ errors

### 7.2 Example: Simple Parity Code

```
Original: 1011 → With parity: 10111 (odd parity)
Corrupted:       → 10101
                    ≠ ≠
Detected: Hamming distance from valid codewords is odd
```

### 7.3 Hamming(7,4) Code

Encodes 4 data bits into 7 bits, can correct single-bit errors:

```
Data bits:     d1 d2 d3 d4
Parity bits:   p1 p2 p3

Position:   1  2  3  4  5  6  7
           p1 p2 d1 p3 d2 d3 d4

p1 covers positions: 1, 3, 5, 7 (binary: ...1)
p2 covers positions: 2, 3, 6, 7 (binary: ..1.)
p3 covers positions: 4, 5, 6, 7 (binary: .1..)
```

## 8. Real-World Software Engineering Applications

### 8.1 Industry Use Cases

1. **Error-Correcting Codes (ECC Memory)**
   - DRAM with ECC uses Hamming codes
   - Detects and corrects single-bit errors
   - Critical for servers and data centers

2. **Network Communication**
   - Error detection in packets
   - Bluetooth uses Hamming distance for pairing
   - WiFi FEC (Forward Error Correction)

3. **Barcode and QR Codes**
   - Reed-Solomon codes based on Hamming distance
   - Tolerates damage and smudges
   - ISBN check digits

4. **DNA Analysis**
   - Measure genetic mutations
   - SNP (Single Nucleotide Polymorphism) detection
   - Species similarity

5. **Cryptography**
   - Measuring randomness of hash functions
   - Differential cryptanalysis
   - Key comparison

6. **Machine Learning**
   - Locality-Sensitive Hashing (LSH)
   - Near-duplicate detection
   - SimHash for document similarity

### 8.2 Production Example: SimHash

```python
import hashlib
from collections import Counter

class SimHash:
    """
    SimHash for near-duplicate detection using Hamming distance.
    Used by Google for web page deduplication.
    """
    
    def __init__(self, num_bits: int = 64):
        self.num_bits = num_bits
    
    def _tokenize(self, text: str) -> list[str]:
        """Simple word tokenization."""
        return text.lower().split()
    
    def _hash_token(self, token: str) -> int:
        """Hash a token to num_bits integer."""
        h = hashlib.md5(token.encode()).digest()
        return int.from_bytes(h[:8], 'big') % (1 << self.num_bits)
    
    def compute(self, text: str) -> int:
        """
        Compute SimHash fingerprint of text.
        
        >>> sh = SimHash(64)
        >>> fp1 = sh.compute("the quick brown fox")
        >>> fp2 = sh.compute("the quick brown dog")
        >>> sh.hamming_distance(fp1, fp2) < 10  # Similar texts
        True
        """
        tokens = self._tokenize(text)
        weights = Counter(tokens)
        
        v = [0] * self.num_bits
        
        for token, weight in weights.items():
            h = self._hash_token(token)
            for i in range(self.num_bits):
                if h & (1 << i):
                    v[i] += weight
                else:
                    v[i] -= weight
        
        fingerprint = 0
        for i in range(self.num_bits):
            if v[i] > 0:
                fingerprint |= (1 << i)
        
        return fingerprint
    
    def hamming_distance(self, fp1: int, fp2: int) -> int:
        """Calculate Hamming distance between fingerprints."""
        xor = fp1 ^ fp2
        distance = 0
        while xor:
            distance += 1
            xor &= xor - 1
        return distance
    
    def is_near_duplicate(self, text1: str, text2: str, 
                         threshold: int = 3) -> bool:
        """Check if two texts are near-duplicates."""
        fp1 = self.compute(text1)
        fp2 = self.compute(text2)
        return self.hamming_distance(fp1, fp2) <= threshold


# Usage
simhash = SimHash()
text1 = "The quick brown fox jumps over the lazy dog"
text2 = "The quick brown fox leaps over the lazy dog"
text3 = "Lorem ipsum dolor sit amet consectetur"

print(simhash.is_near_duplicate(text1, text2))  # True
print(simhash.is_near_duplicate(text1, text3))  # False
```

### 8.3 ECC Memory Simulation

```python
class HammingCode:
    """
    Hamming(7,4) encoder/decoder for single-bit error correction.
    """
    
    @staticmethod
    def encode(data: int) -> int:
        """
        Encode 4 data bits into 7-bit Hamming code.
        
        >>> bin(HammingCode.encode(0b1011))
        '0b1011010'
        """
        if data < 0 or data > 15:
            raise ValueError("Data must be 4 bits (0-15)")
        
        # Extract data bits
        d1 = (data >> 3) & 1
        d2 = (data >> 2) & 1
        d3 = (data >> 1) & 1
        d4 = data & 1
        
        # Calculate parity bits
        p1 = d1 ^ d2 ^ d4  # positions 1,3,5,7
        p2 = d1 ^ d3 ^ d4  # positions 2,3,6,7
        p3 = d2 ^ d3 ^ d4  # positions 4,5,6,7
        
        # Assemble codeword: p1 p2 d1 p3 d2 d3 d4
        codeword = (p1 << 6) | (p2 << 5) | (d1 << 4) | \
                   (p3 << 3) | (d2 << 2) | (d3 << 1) | d4
        
        return codeword
    
    @staticmethod
    def decode(codeword: int) -> tuple[int, int]:
        """
        Decode 7-bit Hamming code, correcting single-bit errors.
        Returns (data, error_position) where error_position=0 means no error.
        
        >>> HammingCode.decode(0b1011010)
        (11, 0)
        >>> HammingCode.decode(0b1011011)  # Bit 0 flipped
        (11, 1)
        """
        # Extract bits
        bits = [(codeword >> i) & 1 for i in range(6, -1, -1)]
        p1, p2, d1, p3, d2, d3, d4 = bits
        
        # Calculate syndrome
        s1 = p1 ^ d1 ^ d2 ^ d4
        s2 = p2 ^ d1 ^ d3 ^ d4
        s3 = p3 ^ d2 ^ d3 ^ d4
        
        error_pos = (s3 << 2) | (s2 << 1) | s1
        
        # Correct error if needed
        if error_pos:
            bit_index = 7 - error_pos
            codeword ^= (1 << bit_index)
            bits = [(codeword >> i) & 1 for i in range(6, -1, -1)]
            p1, p2, d1, p3, d2, d3, d4 = bits
        
        # Extract data
        data = (d1 << 3) | (d2 << 2) | (d3 << 1) | d4
        
        return data, error_pos


# Usage
original = 0b1011  # 11 in decimal
encoded = HammingCode.encode(original)
print(f"Original: {original:04b}, Encoded: {encoded:07b}")

# Simulate single-bit error
corrupted = encoded ^ 0b0001000  # Flip bit 3
decoded, error_pos = HammingCode.decode(corrupted)
print(f"Corrupted: {corrupted:07b}, Decoded: {decoded:04b}, Error at: {error_pos}")
```

## 9. Comparison with Other Distance Metrics

| Metric | Requirement | Handles |
|--------|-------------|---------|
| **Hamming** | Equal length | Substitutions only |
| Levenshtein | Any length | Insert, delete, substitute |
| Jaccard | Sets | Set similarity |
| Cosine | Vectors | Angle between vectors |

## 10. Edge Cases

| Case | Distance | Notes |
|------|----------|-------|
| Empty strings | 0 | Trivial |
| Identical | 0 | No differences |
| Completely different | n | All positions differ |
| Unequal length | Error | Not defined |

## 11. References

- Hamming, R.W. (1950). "Error detecting and error correcting codes"
- [Wikipedia: Hamming Distance](https://en.wikipedia.org/wiki/Hamming_distance)
- MacWilliams, F.J., Sloane, N.J.A. (1977). "The Theory of Error-Correcting Codes"
