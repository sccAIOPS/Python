# Binary Number Representations

## Overview
- **Category**: Bit Manipulation / Number Systems
- **Complexity**: O(n) for n-bit numbers
- **Type**: Encoding, conversion algorithms
- **Source Files**: [bit_manipulation/](../../../bit_manipulation/)

## 1. Mathematical Foundation

### 1.1 Positional Number Systems

A number in base $b$ with digits $d_n, d_{n-1}, ..., d_1, d_0$:
$$
N = \sum_{i=0}^{n} d_i \cdot b^i
$$

### 1.2 Binary (Base 2)

Standard binary representation:
$$
N = \sum_{i=0}^{n} b_i \cdot 2^i, \quad b_i \in \{0, 1\}
$$

### 1.3 Two's Complement

For signed integers in $n$ bits:
$$
N = -b_{n-1} \cdot 2^{n-1} + \sum_{i=0}^{n-2} b_i \cdot 2^i
$$

Range: $[-2^{n-1}, 2^{n-1} - 1]$

**Conversion to negative:**
1. Invert all bits (one's complement)
2. Add 1

### 1.4 Binary Coded Decimal (BCD)

Each decimal digit encoded separately in 4 bits:
$$
BCD(d) = \begin{cases}
0000 & d = 0 \\
0001 & d = 1 \\
\vdots \\
1001 & d = 9
\end{cases}
$$

### 1.5 Gray Code

Adjacent values differ by exactly one bit:
$$
G_i = B_i \oplus B_{i+1}
$$

Where $B_i$ is binary bit $i$, $\oplus$ is XOR.

**Conversion formulas:**
- Binary to Gray: $G = B \oplus (B >> 1)$
- Gray to Binary: $B_i = \bigoplus_{j=i}^{n-1} G_j$

### 1.6 Excess-K (Biased) Representation

Add bias $K$ to the value:
$$
Excess_K(N) = N + K
$$

Common: Excess-3 for BCD arithmetic, Excess-127 for IEEE 754 exponents.

### 1.7 One's Complement

Negative numbers by inverting all bits:
$$
-N = \overline{N}
$$

Has two zeros: +0 (all 0s) and -0 (all 1s).

## 2. Representation Types

### 2.1 Unsigned Binary
- Range: $[0, 2^n - 1]$
- Direct positional representation

### 2.2 Signed Representations
- **Sign-Magnitude**: MSB = sign, rest = magnitude
- **One's Complement**: Invert bits for negative
- **Two's Complement**: Standard, one representation of zero

### 2.3 BCD Variants
- **8421 BCD**: Standard weighting
- **Excess-3**: Add 3 to each digit
- **2421 BCD**: Alternative weights
- **Gray BCD**: Gray-coded BCD

### 2.4 Floating Point
- **IEEE 754**: Sign + Exponent + Mantissa
- Single precision: 1 + 8 + 23 bits
- Double precision: 1 + 11 + 52 bits

## 3. Pseudocode

### 3.1 Binary to Gray Code

```
ALGORITHM BinaryToGray(binary)
    INPUT: Binary number
    OUTPUT: Gray code equivalent
    
    return binary XOR (binary >> 1)
```

### 3.2 Gray Code to Binary

```
ALGORITHM GrayToBinary(gray)
    INPUT: Gray code number
    OUTPUT: Binary equivalent
    
    binary ← gray
    mask ← gray >> 1
    
    while mask ≠ 0 do
        binary ← binary XOR mask
        mask ← mask >> 1
    
    return binary
```

### 3.3 Decimal to BCD

```
ALGORITHM DecimalToBCD(decimal)
    INPUT: Decimal number
    OUTPUT: BCD representation
    
    bcd ← 0
    shift ← 0
    
    while decimal > 0 do
        digit ← decimal MOD 10
        bcd ← bcd OR (digit << shift)
        decimal ← decimal / 10
        shift ← shift + 4
    
    return bcd
```

### 3.4 BCD to Decimal

```
ALGORITHM BCDToDecimal(bcd)
    INPUT: BCD number
    OUTPUT: Decimal equivalent
    
    decimal ← 0
    multiplier ← 1
    
    while bcd > 0 do
        digit ← bcd AND 0xF
        decimal ← decimal + digit * multiplier
        bcd ← bcd >> 4
        multiplier ← multiplier * 10
    
    return decimal
```

### 3.5 Two's Complement Conversion

```
ALGORITHM ToTwosComplement(value, bits)
    INPUT: Signed value, number of bits
    OUTPUT: Two's complement representation
    
    if value ≥ 0 then
        return value
    else
        return (1 << bits) + value


ALGORITHM FromTwosComplement(binary, bits)
    INPUT: Binary value, number of bits
    OUTPUT: Signed integer
    
    if (binary >> (bits - 1)) = 1 then
        // Negative number
        return binary - (1 << bits)
    else
        return binary
```

### 3.6 Excess-3 Conversion

```
ALGORITHM DecimalToExcess3(decimal)
    INPUT: Decimal number
    OUTPUT: Excess-3 BCD
    
    excess3 ← 0
    shift ← 0
    
    while decimal > 0 do
        digit ← decimal MOD 10
        excess3_digit ← digit + 3
        excess3 ← excess3 OR (excess3_digit << shift)
        decimal ← decimal / 10
        shift ← shift + 4
    
    return excess3


ALGORITHM Excess3ToDecimal(excess3)
    INPUT: Excess-3 BCD
    OUTPUT: Decimal number
    
    decimal ← 0
    multiplier ← 1
    
    while excess3 > 0 do
        digit ← (excess3 AND 0xF) - 3
        decimal ← decimal + digit * multiplier
        excess3 ← excess3 >> 4
        multiplier ← multiplier * 10
    
    return decimal
```

### 3.7 Generate n-bit Gray Code Sequence

```
ALGORITHM GenerateGrayCodeSequence(n)
    INPUT: Number of bits
    OUTPUT: List of 2^n Gray codes
    
    if n = 0 then
        return [0]
    
    // Recursively generate (n-1)-bit sequence
    smaller ← GenerateGrayCodeSequence(n - 1)
    
    result ← []
    
    // First half: prepend 0
    for code in smaller do
        result.append(code)
    
    // Second half: prepend 1, reverse order
    for code in reverse(smaller) do
        result.append(code OR (1 << (n - 1)))
    
    return result
```

## 4. Step-by-Step Examples

### Example 1: Binary to Gray Code

```
Binary: 13 = 1101₂

Step 1: binary = 1101
Step 2: binary >> 1 = 0110
Step 3: XOR operation:
        1101
      ⊕ 0110
        ----
        1011

Gray code: 1011 = 11 (decimal interpretation varies)
```

### Example 2: Gray to Binary

```
Gray: 1011

Step 1: binary = 1011, mask = 0101
        1011 XOR 0101 = 1110

Step 2: mask = 0010
        1110 XOR 0010 = 1100

Step 3: mask = 0001
        1100 XOR 0001 = 1101

Step 4: mask = 0000, stop

Binary: 1101 = 13
```

### Example 3: Decimal 42 to BCD

```
Decimal: 42

Step 1: 42 MOD 10 = 2 → 0010
        42 / 10 = 4

Step 2: 4 MOD 10 = 4 → 0100
        4 / 10 = 0

BCD: 0100 0010 = 0x42
```

### Example 4: Two's Complement of -5 (8-bit)

```
Original: 5 = 0000 0101

Step 1: One's complement (invert)
        1111 1010

Step 2: Add 1
        1111 1010
      +         1
        ---------
        1111 1011

-5 in 8-bit two's complement: 1111 1011 = 0xFB
```

### Example 5: Decimal 27 to Excess-3

```
Decimal: 27

Digit 7: 7 + 3 = 10 = 1010₂
Digit 2: 2 + 3 = 5  = 0101₂

Excess-3: 0101 1010 = 0x5A
```

## 5. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Binary to Gray | O(1) | O(1) |
| Gray to Binary | O(n) | O(1) |
| Decimal to BCD | O(d) | O(1) |
| BCD to Decimal | O(d) | O(1) |
| Two's Complement | O(1) | O(1) |
| Generate Gray sequence | O(2^n) | O(2^n) |

Where n = bits, d = decimal digits

## 6. Visual Representation

### 6.1 Gray Code Property

```
Binary  Gray   Decimal
000     000    0
001     001    1       ↑ 1 bit change
010     011    2       ↑ 1 bit change  
011     010    3       ↑ 1 bit change
100     110    4       ↑ 1 bit change
101     111    5       ↑ 1 bit change
110     101    6       ↑ 1 bit change
111     100    7       ↑ 1 bit change
```

### 6.2 BCD Encoding

```
Decimal: 1 9 8 4
         │ │ │ │
         ▼ ▼ ▼ ▼
BCD:    0001 1001 1000 0100
         1    9    8    4
```

### 6.3 Two's Complement Circle (4-bit)

```
        0000 (0)
       /        \
   1111 (-1)    0001 (1)
      |            |
   1110 (-2)    0010 (2)
      |            |
   1101 (-3)    0011 (3)
      |            |
   1100 (-4)    0100 (4)
      |            |
   1011 (-5)    0101 (5)
      |            |
   1010 (-6)    0110 (6)
       \        /
    1001 (-7) 0111 (7)
         \   /
       1000 (-8)
```

## 7. Implementation

```python
from typing import List, Tuple, Optional
from dataclasses import dataclass


def binary_to_gray(binary: int) -> int:
    """
    Convert binary number to Gray code.
    
    >>> binary_to_gray(0)
    0
    >>> binary_to_gray(1)
    1
    >>> binary_to_gray(2)
    3
    >>> binary_to_gray(7)
    4
    >>> binary_to_gray(13)
    11
    """
    return binary ^ (binary >> 1)


def gray_to_binary(gray: int) -> int:
    """
    Convert Gray code to binary.
    
    >>> gray_to_binary(0)
    0
    >>> gray_to_binary(1)
    1
    >>> gray_to_binary(3)
    2
    >>> gray_to_binary(4)
    7
    >>> gray_to_binary(11)
    13
    """
    binary = gray
    mask = gray >> 1
    
    while mask:
        binary ^= mask
        mask >>= 1
    
    return binary


def generate_gray_code_sequence(n: int) -> List[int]:
    """
    Generate n-bit Gray code sequence.
    
    >>> generate_gray_code_sequence(2)
    [0, 1, 3, 2]
    >>> generate_gray_code_sequence(3)
    [0, 1, 3, 2, 6, 7, 5, 4]
    >>> len(generate_gray_code_sequence(4))
    16
    """
    if n <= 0:
        return [0]
    
    # Iterative approach
    sequence = [0]
    
    for i in range(n):
        # Reflect and prefix with 1
        bit = 1 << i
        for j in range(len(sequence) - 1, -1, -1):
            sequence.append(sequence[j] | bit)
    
    return sequence


def decimal_to_bcd(decimal: int) -> int:
    """
    Convert decimal to BCD (8421).
    
    >>> hex(decimal_to_bcd(42))
    '0x42'
    >>> hex(decimal_to_bcd(99))
    '0x99'
    >>> hex(decimal_to_bcd(123))
    '0x123'
    >>> decimal_to_bcd(0)
    0
    """
    if decimal < 0:
        raise ValueError("BCD requires non-negative integers")
    
    bcd = 0
    shift = 0
    
    while decimal > 0:
        digit = decimal % 10
        bcd |= (digit << shift)
        decimal //= 10
        shift += 4
    
    return bcd


def bcd_to_decimal(bcd: int) -> int:
    """
    Convert BCD to decimal.
    
    >>> bcd_to_decimal(0x42)
    42
    >>> bcd_to_decimal(0x99)
    99
    >>> bcd_to_decimal(0x123)
    123
    >>> bcd_to_decimal(0)
    0
    """
    decimal = 0
    multiplier = 1
    
    while bcd > 0:
        digit = bcd & 0xF
        if digit > 9:
            raise ValueError(f"Invalid BCD digit: {digit}")
        decimal += digit * multiplier
        bcd >>= 4
        multiplier *= 10
    
    return decimal


def is_valid_bcd(bcd: int) -> bool:
    """
    Check if value is valid BCD.
    
    >>> is_valid_bcd(0x42)
    True
    >>> is_valid_bcd(0x99)
    True
    >>> is_valid_bcd(0xAB)
    False
    >>> is_valid_bcd(0x1A)
    False
    """
    while bcd > 0:
        if (bcd & 0xF) > 9:
            return False
        bcd >>= 4
    return True


def decimal_to_excess3(decimal: int) -> int:
    """
    Convert decimal to Excess-3 BCD.
    
    >>> hex(decimal_to_excess3(0))
    '0x3'
    >>> hex(decimal_to_excess3(5))
    '0x8'
    >>> hex(decimal_to_excess3(42))
    '0x75'
    >>> hex(decimal_to_excess3(99))
    '0xcc'
    """
    if decimal < 0:
        raise ValueError("Excess-3 requires non-negative integers")
    
    if decimal == 0:
        return 3  # 0 + 3 = 3
    
    excess3 = 0
    shift = 0
    
    while decimal > 0:
        digit = decimal % 10
        excess3_digit = digit + 3
        excess3 |= (excess3_digit << shift)
        decimal //= 10
        shift += 4
    
    return excess3


def excess3_to_decimal(excess3: int) -> int:
    """
    Convert Excess-3 BCD to decimal.
    
    >>> excess3_to_decimal(0x3)
    0
    >>> excess3_to_decimal(0x8)
    5
    >>> excess3_to_decimal(0x75)
    42
    >>> excess3_to_decimal(0xcc)
    99
    """
    decimal = 0
    multiplier = 1
    
    while excess3 > 0:
        digit = (excess3 & 0xF) - 3
        if digit < 0 or digit > 9:
            raise ValueError(f"Invalid Excess-3 digit")
        decimal += digit * multiplier
        excess3 >>= 4
        multiplier *= 10
    
    return decimal


def to_twos_complement(value: int, bits: int) -> int:
    """
    Convert signed integer to two's complement.
    
    >>> bin(to_twos_complement(5, 8))
    '0b101'
    >>> bin(to_twos_complement(-5, 8))
    '0b11111011'
    >>> bin(to_twos_complement(-1, 8))
    '0b11111111'
    >>> bin(to_twos_complement(-128, 8))
    '0b10000000'
    """
    if value >= 0:
        return value & ((1 << bits) - 1)
    else:
        return (1 << bits) + value


def from_twos_complement(binary: int, bits: int) -> int:
    """
    Convert two's complement to signed integer.
    
    >>> from_twos_complement(0b00000101, 8)
    5
    >>> from_twos_complement(0b11111011, 8)
    -5
    >>> from_twos_complement(0b11111111, 8)
    -1
    >>> from_twos_complement(0b10000000, 8)
    -128
    """
    if binary >> (bits - 1):  # Check sign bit
        return binary - (1 << bits)
    return binary


def to_ones_complement(value: int, bits: int) -> int:
    """
    Convert to one's complement representation.
    
    >>> bin(to_ones_complement(5, 8))
    '0b101'
    >>> bin(to_ones_complement(-5, 8))
    '0b11111010'
    >>> bin(to_ones_complement(-0, 8))
    '0b0'
    """
    if value >= 0:
        return value
    else:
        mask = (1 << bits) - 1
        return mask ^ (-value)


def to_sign_magnitude(value: int, bits: int) -> int:
    """
    Convert to sign-magnitude representation.
    
    >>> bin(to_sign_magnitude(5, 8))
    '0b101'
    >>> bin(to_sign_magnitude(-5, 8))
    '0b10000101'
    >>> bin(to_sign_magnitude(-127, 8))
    '0b11111111'
    """
    if value >= 0:
        return value
    else:
        sign_bit = 1 << (bits - 1)
        return sign_bit | (-value)


def from_sign_magnitude(binary: int, bits: int) -> int:
    """
    Convert from sign-magnitude to integer.
    
    >>> from_sign_magnitude(0b00000101, 8)
    5
    >>> from_sign_magnitude(0b10000101, 8)
    -5
    >>> from_sign_magnitude(0b11111111, 8)
    -127
    """
    sign_bit = 1 << (bits - 1)
    if binary & sign_bit:
        return -(binary & (sign_bit - 1))
    return binary


class FloatingPointIEEE754:
    """
    IEEE 754 floating point representation.
    
    >>> fp = FloatingPointIEEE754()
    >>> fp.decode_single(0x40490FDB)  # Pi approximation
    (0, 128, 4788187)
    >>> fp.encode_single(0, 128, 4788187)
    1078530011
    """
    
    # Single precision: 1 sign + 8 exponent + 23 mantissa
    SINGLE_EXP_BITS = 8
    SINGLE_MANT_BITS = 23
    SINGLE_BIAS = 127
    
    # Double precision: 1 sign + 11 exponent + 52 mantissa
    DOUBLE_EXP_BITS = 11
    DOUBLE_MANT_BITS = 52
    DOUBLE_BIAS = 1023
    
    def decode_single(self, bits: int) -> Tuple[int, int, int]:
        """Decode 32-bit float to (sign, exponent, mantissa)."""
        sign = (bits >> 31) & 1
        exponent = (bits >> 23) & 0xFF
        mantissa = bits & 0x7FFFFF
        return (sign, exponent, mantissa)
    
    def encode_single(self, sign: int, exponent: int, mantissa: int) -> int:
        """Encode to 32-bit float."""
        return (sign << 31) | (exponent << 23) | mantissa
    
    def float_to_single(self, value: float) -> int:
        """Convert float to IEEE 754 single precision bits."""
        import struct
        return struct.unpack('>I', struct.pack('>f', value))[0]
    
    def single_to_float(self, bits: int) -> float:
        """Convert IEEE 754 single precision bits to float."""
        import struct
        return struct.unpack('>f', struct.pack('>I', bits))[0]


def format_binary(value: int, bits: int = 8, group: int = 4) -> str:
    """
    Format integer as binary string with grouping.
    
    >>> format_binary(42, 8, 4)
    '0010 1010'
    >>> format_binary(255, 8, 4)
    '1111 1111'
    >>> format_binary(0, 8, 4)
    '0000 0000'
    """
    binary = bin(value)[2:].zfill(bits)
    groups = [binary[i:i+group] for i in range(0, len(binary), group)]
    return ' '.join(groups)


# Demo
if __name__ == "__main__":
    print("Binary Representations Demo")
    print("=" * 60)
    
    # Gray Code
    print("\n1. Gray Code:")
    for i in range(8):
        gray = binary_to_gray(i)
        back = gray_to_binary(gray)
        print(f"   {i}: binary={bin(i)[2:].zfill(3)} → "
              f"gray={bin(gray)[2:].zfill(3)} → binary={back}")
    
    # BCD
    print("\n2. BCD (Binary Coded Decimal):")
    for n in [42, 99, 123]:
        bcd = decimal_to_bcd(n)
        back = bcd_to_decimal(bcd)
        print(f"   {n} → BCD=0x{bcd:X} → {back}")
    
    # Excess-3
    print("\n3. Excess-3:")
    for n in [0, 5, 42, 99]:
        exc3 = decimal_to_excess3(n)
        back = excess3_to_decimal(exc3)
        print(f"   {n} → Excess-3=0x{exc3:X} → {back}")
    
    # Two's Complement
    print("\n4. Two's Complement (8-bit):")
    for n in [5, -5, -1, -128, 127]:
        tc = to_twos_complement(n, 8)
        back = from_twos_complement(tc, 8)
        print(f"   {n:4d} → {format_binary(tc, 8)} → {back}")
    
    # Sign-Magnitude
    print("\n5. Sign-Magnitude (8-bit):")
    for n in [5, -5, 127, -127]:
        sm = to_sign_magnitude(n, 8)
        back = from_sign_magnitude(sm, 8)
        print(f"   {n:4d} → {format_binary(sm, 8)} → {back}")
    
    # Gray Code Sequence
    print("\n6. 3-bit Gray Code Sequence:")
    seq = generate_gray_code_sequence(3)
    for i, code in enumerate(seq):
        print(f"   {i}: {bin(code)[2:].zfill(3)}")
```

## 8. Applications

### 8.1 Gray Code Applications
- Rotary encoders
- Karnaugh maps
- Error minimization in ADC
- Genetic algorithms

### 8.2 BCD Applications
- Digital clocks and displays
- Financial calculations
- Seven-segment displays
- COBOL decimal arithmetic

### 8.3 Two's Complement Applications
- All modern CPUs
- Integer arithmetic
- Hardware ALU design

## 9. Real-World Software Engineering Applications

### 9.1 Production Example: Sensor Data Encoder

```python
from typing import Dict, List, Optional, Tuple
from dataclasses import dataclass
from enum import Enum
import struct
import time


class EncodingType(Enum):
    """Supported encoding types."""
    BINARY = "binary"
    BCD = "bcd"
    GRAY = "gray"
    EXCESS3 = "excess3"
    TWOS_COMPLEMENT = "twos_complement"


@dataclass
class SensorReading:
    """Sensor data reading."""
    sensor_id: int
    value: int
    timestamp: float
    encoding: EncodingType


class SensorDataEncoder:
    """
    Encodes sensor data using various binary representations.
    
    Used in embedded systems and IoT applications.
    """
    
    def __init__(self, default_bits: int = 16):
        self.default_bits = default_bits
        self._encoding_stats: Dict[EncodingType, int] = {
            e: 0 for e in EncodingType
        }
    
    def encode(
        self,
        value: int,
        encoding: EncodingType,
        bits: Optional[int] = None
    ) -> int:
        """Encode value using specified representation."""
        bits = bits or self.default_bits
        self._encoding_stats[encoding] += 1
        
        if encoding == EncodingType.BINARY:
            return value & ((1 << bits) - 1)
        
        elif encoding == EncodingType.BCD:
            return self._to_bcd(value)
        
        elif encoding == EncodingType.GRAY:
            return value ^ (value >> 1)
        
        elif encoding == EncodingType.EXCESS3:
            return self._to_excess3(value)
        
        elif encoding == EncodingType.TWOS_COMPLEMENT:
            if value >= 0:
                return value & ((1 << bits) - 1)
            return (1 << bits) + value
        
        raise ValueError(f"Unknown encoding: {encoding}")
    
    def decode(
        self,
        encoded: int,
        encoding: EncodingType,
        bits: Optional[int] = None
    ) -> int:
        """Decode value from specified representation."""
        bits = bits or self.default_bits
        
        if encoding == EncodingType.BINARY:
            return encoded
        
        elif encoding == EncodingType.BCD:
            return self._from_bcd(encoded)
        
        elif encoding == EncodingType.GRAY:
            return self._gray_to_binary(encoded)
        
        elif encoding == EncodingType.EXCESS3:
            return self._from_excess3(encoded)
        
        elif encoding == EncodingType.TWOS_COMPLEMENT:
            if encoded >> (bits - 1):
                return encoded - (1 << bits)
            return encoded
        
        raise ValueError(f"Unknown encoding: {encoding}")
    
    def _to_bcd(self, value: int) -> int:
        """Convert to BCD."""
        if value < 0:
            raise ValueError("BCD requires non-negative")
        result = 0
        shift = 0
        while value > 0:
            result |= (value % 10) << shift
            value //= 10
            shift += 4
        return result
    
    def _from_bcd(self, bcd: int) -> int:
        """Convert from BCD."""
        result = 0
        mult = 1
        while bcd > 0:
            digit = bcd & 0xF
            if digit > 9:
                raise ValueError(f"Invalid BCD: {digit}")
            result += digit * mult
            bcd >>= 4
            mult *= 10
        return result
    
    def _to_excess3(self, value: int) -> int:
        """Convert to Excess-3."""
        if value < 0:
            raise ValueError("Excess-3 requires non-negative")
        if value == 0:
            return 3
        result = 0
        shift = 0
        while value > 0:
            result |= ((value % 10) + 3) << shift
            value //= 10
            shift += 4
        return result
    
    def _from_excess3(self, exc3: int) -> int:
        """Convert from Excess-3."""
        result = 0
        mult = 1
        while exc3 > 0:
            digit = (exc3 & 0xF) - 3
            if digit < 0 or digit > 9:
                raise ValueError("Invalid Excess-3")
            result += digit * mult
            exc3 >>= 4
            mult *= 10
        return result
    
    def _gray_to_binary(self, gray: int) -> int:
        """Convert Gray to binary."""
        binary = gray
        mask = gray >> 1
        while mask:
            binary ^= mask
            mask >>= 1
        return binary
    
    def get_stats(self) -> Dict[str, int]:
        """Get encoding usage statistics."""
        return {e.value: c for e, c in self._encoding_stats.items()}


class RotaryEncoderSimulator:
    """
    Simulates a rotary encoder using Gray code.
    
    Gray code ensures only one bit changes between adjacent positions,
    eliminating potential read errors during transitions.
    """
    
    def __init__(self, resolution_bits: int = 8):
        self.resolution_bits = resolution_bits
        self.positions = 1 << resolution_bits
        self.current_position = 0
        self._gray_table = self._build_gray_table()
    
    def _build_gray_table(self) -> List[int]:
        """Build Gray code lookup table."""
        return [i ^ (i >> 1) for i in range(self.positions)]
    
    def get_position(self) -> int:
        """Get current position (binary)."""
        return self.current_position
    
    def get_gray_code(self) -> int:
        """Get current position as Gray code."""
        return self._gray_table[self.current_position]
    
    def rotate(self, steps: int) -> int:
        """Rotate encoder by steps (positive = clockwise)."""
        self.current_position = (
            self.current_position + steps
        ) % self.positions
        return self.current_position
    
    def read_and_decode(self, gray_reading: int) -> int:
        """
        Decode Gray code reading to position.
        
        In hardware, this would read the actual encoder.
        """
        binary = gray_reading
        mask = gray_reading >> 1
        while mask:
            binary ^= mask
            mask >>= 1
        return binary
    
    def get_direction(self, prev_gray: int, curr_gray: int) -> int:
        """
        Determine rotation direction from Gray code readings.
        
        Returns: 1 for CW, -1 for CCW, 0 for same position
        """
        prev_pos = self.read_and_decode(prev_gray)
        curr_pos = self.read_and_decode(curr_gray)
        
        diff = curr_pos - prev_pos
        
        # Handle wraparound
        if diff > self.positions // 2:
            return -1
        elif diff < -self.positions // 2:
            return 1
        elif diff > 0:
            return 1
        elif diff < 0:
            return -1
        return 0


class FinancialCalculator:
    """
    BCD-based financial calculator for exact decimal arithmetic.
    
    Uses BCD to avoid floating-point precision issues.
    """
    
    def __init__(self, decimal_places: int = 2):
        self.decimal_places = decimal_places
        self.scale = 10 ** decimal_places
    
    def to_internal(self, amount: float) -> int:
        """Convert float to internal BCD representation."""
        scaled = int(round(amount * self.scale))
        return self._decimal_to_bcd(scaled)
    
    def to_external(self, bcd: int) -> float:
        """Convert BCD to float for display."""
        scaled = self._bcd_to_decimal(bcd)
        return scaled / self.scale
    
    def _decimal_to_bcd(self, value: int) -> int:
        """Convert integer to BCD."""
        negative = value < 0
        value = abs(value)
        
        result = 0
        shift = 0
        while value > 0:
            result |= (value % 10) << shift
            value //= 10
            shift += 4
        
        if negative:
            result |= (0xD << shift)  # Sign nibble
        
        return result
    
    def _bcd_to_decimal(self, bcd: int) -> int:
        """Convert BCD to integer."""
        negative = False
        result = 0
        mult = 1
        
        while bcd > 0:
            digit = bcd & 0xF
            if digit == 0xD:  # Sign nibble
                negative = True
                break
            if digit > 9:
                raise ValueError(f"Invalid BCD digit: {digit}")
            result += digit * mult
            bcd >>= 4
            mult *= 10
        
        return -result if negative else result
    
    def add(self, bcd_a: int, bcd_b: int) -> int:
        """Add two BCD amounts."""
        a = self._bcd_to_decimal(bcd_a)
        b = self._bcd_to_decimal(bcd_b)
        return self._decimal_to_bcd(a + b)
    
    def subtract(self, bcd_a: int, bcd_b: int) -> int:
        """Subtract BCD amounts."""
        a = self._bcd_to_decimal(bcd_a)
        b = self._bcd_to_decimal(bcd_b)
        return self._decimal_to_bcd(a - b)
    
    def multiply(self, bcd_amount: int, factor: float) -> int:
        """Multiply BCD amount by factor."""
        amount = self._bcd_to_decimal(bcd_amount)
        result = int(round(amount * factor))
        return self._decimal_to_bcd(result)
    
    def format_currency(self, bcd: int, symbol: str = "$") -> str:
        """Format BCD as currency string."""
        amount = self.to_external(bcd)
        return f"{symbol}{amount:,.{self.decimal_places}f}"


class DataPacketEncoder:
    """
    Encodes data packets with various integer representations.
    
    Used in communication protocols and data serialization.
    """
    
    def __init__(self):
        self.packets_encoded = 0
        self.total_bytes = 0
    
    def encode_packet(
        self,
        data: Dict[str, int],
        spec: Dict[str, Tuple[int, str]]
    ) -> bytes:
        """
        Encode data packet according to specification.
        
        Args:
            data: Field name to value mapping
            spec: Field name to (bits, encoding) mapping
        
        Returns:
            Encoded bytes
        """
        bits = []
        
        for field, value in data.items():
            if field not in spec:
                raise ValueError(f"Unknown field: {field}")
            
            field_bits, encoding = spec[field]
            encoded = self._encode_field(value, field_bits, encoding)
            
            # Convert to bit string
            bit_str = bin(encoded)[2:].zfill(field_bits)
            bits.append(bit_str)
        
        # Combine and pad to byte boundary
        all_bits = ''.join(bits)
        padding = (8 - len(all_bits) % 8) % 8
        all_bits = all_bits + '0' * padding
        
        # Convert to bytes
        result = bytes(
            int(all_bits[i:i+8], 2)
            for i in range(0, len(all_bits), 8)
        )
        
        self.packets_encoded += 1
        self.total_bytes += len(result)
        
        return result
    
    def _encode_field(
        self,
        value: int,
        bits: int,
        encoding: str
    ) -> int:
        """Encode single field."""
        if encoding == "unsigned":
            return value & ((1 << bits) - 1)
        
        elif encoding == "twos":
            if value >= 0:
                return value & ((1 << bits) - 1)
            return (1 << bits) + value
        
        elif encoding == "gray":
            return value ^ (value >> 1)
        
        elif encoding == "bcd":
            result = 0
            shift = 0
            while value > 0:
                result |= (value % 10) << shift
                value //= 10
                shift += 4
            return result
        
        raise ValueError(f"Unknown encoding: {encoding}")
    
    def decode_packet(
        self,
        data: bytes,
        spec: Dict[str, Tuple[int, str]]
    ) -> Dict[str, int]:
        """Decode data packet according to specification."""
        # Convert bytes to bit string
        bits = ''.join(bin(b)[2:].zfill(8) for b in data)
        
        result = {}
        offset = 0
        
        for field, (field_bits, encoding) in spec.items():
            field_data = int(bits[offset:offset + field_bits], 2)
            result[field] = self._decode_field(
                field_data, field_bits, encoding
            )
            offset += field_bits
        
        return result
    
    def _decode_field(
        self,
        encoded: int,
        bits: int,
        encoding: str
    ) -> int:
        """Decode single field."""
        if encoding == "unsigned":
            return encoded
        
        elif encoding == "twos":
            if encoded >> (bits - 1):
                return encoded - (1 << bits)
            return encoded
        
        elif encoding == "gray":
            binary = encoded
            mask = encoded >> 1
            while mask:
                binary ^= mask
                mask >>= 1
            return binary
        
        elif encoding == "bcd":
            result = 0
            mult = 1
            while encoded > 0:
                result += (encoded & 0xF) * mult
                encoded >>= 4
                mult *= 10
            return result
        
        raise ValueError(f"Unknown encoding: {encoding}")


# Demo
if __name__ == "__main__":
    print("Binary Representations Production Demo")
    print("=" * 60)
    
    # Sensor Encoder
    print("\n1. Sensor Data Encoder:")
    encoder = SensorDataEncoder(bits=16)
    
    for encoding in EncodingType:
        value = 42
        encoded = encoder.encode(value, encoding)
        decoded = encoder.decode(encoded, encoding)
        print(f"   {encoding.value}: {value} → 0x{encoded:04X} → {decoded}")
    
    # Rotary Encoder
    print("\n2. Rotary Encoder Simulator:")
    rotary = RotaryEncoderSimulator(resolution_bits=4)
    
    for _ in range(5):
        pos = rotary.get_position()
        gray = rotary.get_gray_code()
        print(f"   Position {pos:2d}: Gray={bin(gray)[2:].zfill(4)}")
        rotary.rotate(3)
    
    # Financial Calculator
    print("\n3. Financial Calculator (BCD):")
    calc = FinancialCalculator(decimal_places=2)
    
    price = calc.to_internal(99.99)
    tax = calc.multiply(price, 0.08)
    total = calc.add(price, tax)
    
    print(f"   Price: {calc.format_currency(price)}")
    print(f"   Tax (8%): {calc.format_currency(tax)}")
    print(f"   Total: {calc.format_currency(total)}")
    
    # Data Packet
    print("\n4. Data Packet Encoding:")
    packet_encoder = DataPacketEncoder()
    
    spec = {
        "sensor_id": (8, "unsigned"),
        "temperature": (12, "twos"),
        "position": (8, "gray"),
        "reading": (16, "bcd")
    }
    
    data = {
        "sensor_id": 42,
        "temperature": -15,
        "position": 127,
        "reading": 9876
    }
    
    encoded = packet_encoder.encode_packet(data, spec)
    decoded = packet_encoder.decode_packet(encoded, spec)
    
    print(f"   Original: {data}")
    print(f"   Encoded: {encoded.hex()}")
    print(f"   Decoded: {decoded}")
```

## 10. Comparison

| Representation | Range (n bits) | Zeros | Use Case |
|----------------|---------------|-------|----------|
| Unsigned | [0, 2ⁿ-1] | One | Natural numbers |
| Sign-Magnitude | [-(2ⁿ⁻¹-1), 2ⁿ⁻¹-1] | Two | Historical |
| One's Complement | [-(2ⁿ⁻¹-1), 2ⁿ⁻¹-1] | Two | Checksums |
| Two's Complement | [-2ⁿ⁻¹, 2ⁿ⁻¹-1] | One | Modern CPUs |
| BCD | Decimal digits | One | Financial |
| Gray | [0, 2ⁿ-1] | One | Encoders |
| Excess-K | [-K, 2ⁿ-1-K] | One | Exponents |

## 11. Best Practices

1. **Use appropriate representation**: Two's complement for arithmetic, BCD for decimal display
2. **Validate BCD digits**: Check each nibble ≤ 9
3. **Consider overflow**: Different ranges for different representations
4. **Gray code for hardware**: Prevents glitches in transitions
5. **Document bit widths**: Always specify number of bits

## 12. References

- [Wikipedia: Binary-coded decimal](https://en.wikipedia.org/wiki/Binary-coded_decimal)
- [Wikipedia: Gray code](https://en.wikipedia.org/wiki/Gray_code)
- [Wikipedia: Two's complement](https://en.wikipedia.org/wiki/Two%27s_complement)
- Hennessy & Patterson, "Computer Organization and Design"
