# Specialized Algorithms

This section contains comprehensive documentation for specialized algorithms implemented in TheAlgorithms/Python repository, covering bit manipulation, cryptography, compression, scheduling, and other domain-specific algorithms.

## Categories

### Bit Manipulation

| Algorithm | Description | Complexity |
|-----------|-------------|------------|
| [Bit Operations](bit_manipulation.md) | Basic bit operations, counting, and shifts | O(1) - O(log n) |
| [Binary Representations](binary_representations.md) | BCD, Gray code, excess-3 | O(n) |

### Cryptography & Ciphers

| Algorithm | Description | Complexity |
|-----------|-------------|------------|
| [Classical Ciphers](classical_ciphers.md) | Caesar, Vigenère, substitution ciphers | O(n) |
| [Modern Cryptography](modern_cryptography.md) | RSA, AES, hashing algorithms | Varies |

### Compression

| Algorithm | Description | Complexity |
|-----------|-------------|------------|
| [Lossless Compression](lossless_compression.md) | Run-length, LZ77, LZW | O(n) - O(n log n) |

### Scheduling

| Algorithm | Description | Complexity |
|-----------|-------------|------------|
| [CPU Scheduling](cpu_scheduling.md) | FCFS, SJF, Round Robin, Priority | O(n) - O(n log n) |

### Other Algorithms

| Algorithm | Description | Complexity |
|-----------|-------------|------------|
| [Cellular Automata](cellular_automata.md) | Conway's Game of Life, Rule 110 | O(n×m) |
| [Fractals](fractals.md) | Mandelbrot, Sierpinski, Koch | O(iterations) |

## Algorithm Overview

### Bit Manipulation

Bit manipulation algorithms operate directly on binary representations:

```python
# Common operations
x & y    # AND - both bits are 1
x | y    # OR - at least one bit is 1
x ^ y    # XOR - bits are different
~x       # NOT - flip all bits
x << n   # Left shift - multiply by 2^n
x >> n   # Right shift - divide by 2^n
```

**Applications**:
- Flag management
- Power of 2 checks
- Efficient arithmetic
- Cryptography
- Error detection/correction

### Cryptography

| Type | Examples | Security |
|------|----------|----------|
| Symmetric | AES, DES, Blowfish | Same key for encrypt/decrypt |
| Asymmetric | RSA, ECC | Public/private key pairs |
| Hashing | SHA-256, MD5 | One-way transformation |
| Classical | Caesar, Vigenère | Educational (not secure) |

### Compression

| Type | Algorithms | Use Case |
|------|------------|----------|
| Lossless | LZ77, Huffman, LZW | Text, archives |
| Lossy | JPEG, MP3 | Media files |
| Dictionary | LZW, LZ78 | Repeating patterns |
| Statistical | Huffman, Arithmetic | Symbol frequencies |

### Scheduling

| Algorithm | Policy | Fairness | Efficiency |
|-----------|--------|----------|------------|
| FCFS | First come, first served | High | Low |
| SJF | Shortest job first | Low | High |
| Round Robin | Time slicing | High | Medium |
| Priority | Priority-based | Low | Variable |

## Complexity Overview

### Bit Operations

| Operation | Time | Space |
|-----------|------|-------|
| AND, OR, XOR, NOT | O(1) | O(1) |
| Shift | O(1) | O(1) |
| Count set bits | O(log n) | O(1) |
| Find rightmost set bit | O(1) | O(1) |

### Cryptographic Operations

| Operation | Complexity |
|-----------|------------|
| AES encrypt/decrypt | O(n) |
| RSA key generation | O(k³) for k-bit key |
| RSA encrypt/decrypt | O(k²) |
| SHA-256 | O(n) |

### Compression

| Algorithm | Compression | Decompression |
|-----------|-------------|---------------|
| Run-length | O(n) | O(n) |
| Huffman | O(n log n) | O(n) |
| LZ77/LZW | O(n) | O(n) |

## Directory Structure

```
10_specialized/
├── index.md                    # This file
├── bit_manipulation.md         # Bit operations
├── binary_representations.md   # BCD, Gray code
├── classical_ciphers.md        # Caesar, Vigenère
├── modern_cryptography.md      # RSA, AES
├── lossless_compression.md     # LZ, RLE, LZW
├── cpu_scheduling.md           # Process scheduling
├── cellular_automata.md        # Game of Life
└── fractals.md                 # Mandelbrot, Sierpinski
```

## See Also

- [Bit Manipulation Source](../../../bit_manipulation/)
- [Ciphers Source](../../../ciphers/)
- [Data Compression Source](../../../data_compression/)
- [Scheduling Source](../../../scheduling/)
- [Cellular Automata Source](../../../cellular_automata/)
- [Fractals Source](../../../fractals/)
