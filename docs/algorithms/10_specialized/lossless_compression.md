# Lossless Compression Algorithms

## Overview
- **Category**: Data Compression / Information Theory
- **Complexity**: Time: O(n) to O(n log n) | Space: O(n)
- **Type**: Entropy coding, dictionary-based compression
- **Source Files**: [data_compression/](../../../data_compression/)

## 1. Mathematical Foundation

### 1.1 Information Theory

**Shannon Entropy** - minimum bits per symbol:
$$
H(X) = -\sum_{i=1}^{n} p_i \log_2(p_i)
$$

Where $p_i$ is the probability of symbol $i$.

### 1.2 Huffman Coding

Optimal prefix-free code minimizing:
$$
L = \sum_{i=1}^{n} p_i \cdot l_i
$$

Where $l_i$ is code length for symbol $i$.

### 1.3 Lempel-Ziv Complexity

LZ77/LZ78 use dictionary of previously seen patterns:
$$
\text{Compression Ratio} = \frac{\text{Original Size}}{\text{Compressed Size}}
$$

### 1.4 Run-Length Encoding

For sequences with runs:
$$
\text{Encoded} = (symbol_1, count_1), (symbol_2, count_2), ...
$$

### 1.5 Burrows-Wheeler Transform

Transforms text for better compression:
$$
BWT(T) = \text{last column of sorted rotations of } T\$
$$

## 2. Algorithm Categories

### 2.1 Entropy Coding
- **Huffman Coding**: Optimal prefix codes
- **Arithmetic Coding**: Fractional bits
- **ANS (Asymmetric Numeral Systems)**: Modern alternative

### 2.2 Dictionary-Based
- **LZ77**: Sliding window dictionary
- **LZ78**: Explicit dictionary
- **LZW**: LZ78 with implicit dictionary

### 2.3 Transform-Based
- **BWT**: Burrows-Wheeler Transform
- **MTF**: Move-to-Front encoding
- **RLE**: Run-Length Encoding

## 3. Pseudocode

### 3.1 Huffman Coding

```
ALGORITHM BuildHuffmanTree(frequencies)
    INPUT: Map of symbol → frequency
    OUTPUT: Root of Huffman tree
    
    heap ← MinHeap()
    for each (symbol, freq) in frequencies do
        heap.insert(Node(symbol, freq))
    
    while heap.size() > 1 do
        left ← heap.extractMin()
        right ← heap.extractMin()
        merged ← Node(null, left.freq + right.freq)
        merged.left ← left
        merged.right ← right
        heap.insert(merged)
    
    return heap.extractMin()


ALGORITHM GenerateCodes(node, code, codes)
    if node is leaf then
        codes[node.symbol] ← code
        return
    
    GenerateCodes(node.left, code + "0", codes)
    GenerateCodes(node.right, code + "1", codes)
```

### 3.2 LZ77 Compression

```
ALGORITHM LZ77Compress(text, window_size)
    INPUT: Text string, sliding window size
    OUTPUT: List of (offset, length, next_char) tuples
    
    output ← []
    i ← 0
    
    while i < length(text) do
        best_offset ← 0
        best_length ← 0
        
        // Search in sliding window
        start ← max(0, i - window_size)
        for j ← start to i - 1 do
            length ← 0
            while i + length < length(text) 
                  AND text[j + length] = text[i + length] do
                length ← length + 1
            
            if length > best_length then
                best_offset ← i - j
                best_length ← length
        
        if i + best_length < length(text) then
            next_char ← text[i + best_length]
        else
            next_char ← EOF
        
        output.append((best_offset, best_length, next_char))
        i ← i + best_length + 1
    
    return output
```

### 3.3 Run-Length Encoding

```
ALGORITHM RLEEncode(data)
    INPUT: Sequence of symbols
    OUTPUT: Encoded sequence
    
    if data is empty then
        return []
    
    output ← []
    current ← data[0]
    count ← 1
    
    for i ← 1 to length(data) - 1 do
        if data[i] = current then
            count ← count + 1
        else
            output.append((current, count))
            current ← data[i]
            count ← 1
    
    output.append((current, count))
    return output
```

### 3.4 Burrows-Wheeler Transform

```
ALGORITHM BWT(text)
    INPUT: Text string
    OUTPUT: Transformed string, original position
    
    text ← text + '$'  // End marker
    n ← length(text)
    
    // Generate all rotations
    rotations ← []
    for i ← 0 to n - 1 do
        rotation ← text[i:] + text[:i]
        rotations.append((rotation, i))
    
    // Sort rotations
    sort rotations by rotation string
    
    // Extract last column
    last_column ← ""
    for (rotation, _) in rotations do
        last_column ← last_column + rotation[n - 1]
    
    // Find original position
    original_index ← index where rotations[index][1] = 0
    
    return (last_column, original_index)
```

## 4. Step-by-Step Example

### Example 1: Huffman Coding

```
Input: "ABRACADABRA"

Step 1: Count frequencies
  A: 5, B: 2, R: 2, C: 1, D: 1

Step 2: Build priority queue (min-heap)
  [C:1, D:1, B:2, R:2, A:5]

Step 3: Build tree
  Merge C(1) + D(1) = CD(2)
  Merge CD(2) + B(2) = CDB(4)
  Merge R(2) + CDB(4) = RCDB(6)  -- actually merge smallest
  
  Final tree:
         (11)
        /    \
      A(5)  (6)
           /   \
        (2)    (4)
        /\     /  \
      R  B   C   D

Step 4: Generate codes
  A: 0
  R: 100
  B: 101
  C: 110
  D: 111

Step 5: Encode
  A B R A C A D A B R A
  0 101 100 0 110 0 111 0 101 100 0

Encoded: 23 bits (vs 88 bits for 8-bit ASCII)
Compression ratio: 3.8:1
```

### Example 2: LZ77

```
Input: "ABABABAB"
Window size: 4

Position 0: A
  No match in window
  Output: (0, 0, 'A')
  
Position 1: B
  No match
  Output: (0, 0, 'B')
  
Position 2: ABABAB
  Match "AB" at offset 2, length 2
  Next char: 'A'
  Output: (2, 2, 'A')
  
Position 5: BAB
  Match "BA" at offset 2, length 2
  Next char: 'B'
  Output: (2, 2, 'B')

Final: [(0,0,'A'), (0,0,'B'), (2,2,'A'), (2,2,'B')]
```

### Example 3: BWT

```
Input: "BANANA"

Step 1: Add end marker
  BANANA$

Step 2: All rotations
  BANANA$  (0)
  ANANA$B  (1)
  NANA$BA  (2)
  ANA$BAN  (3)
  NA$BANA  (4)
  A$BANAN  (5)
  $BANANA  (6)

Step 3: Sort rotations
  $BANANA  (6)
  A$BANAN  (5)
  ANA$BAN  (3)
  ANANA$B  (1)
  BANANA$  (0)  ← original position = 4
  NA$BANA  (4)
  NANA$BA  (2)

Step 4: Last column
  BWT = "ANNB$AA"
  Original index = 4
```

## 5. Complexity Analysis

| Algorithm | Encode Time | Decode Time | Space |
|-----------|-------------|-------------|-------|
| Huffman | O(n log k) | O(n) | O(k) |
| LZ77 | O(n × w) | O(n) | O(w) |
| LZW | O(n) | O(n) | O(dict) |
| RLE | O(n) | O(n) | O(1) |
| BWT | O(n log n) | O(n) | O(n) |

Where n = input length, k = alphabet size, w = window size

## 6. Visual Representation

### 6.1 Huffman Tree

```
Building tree for "ABRACADABRA":

Frequencies: A:5, B:2, R:2, C:1, D:1

     ┌────(11)────┐
     │            │
   ┌─A(5)    ┌──(6)──┐
   │         │       │
             │    ┌─(4)─┐
          ┌─(2)─┐   │   │
          │     │  C(1) D(1)
         R(2)  B(2)

Codes: A=0, R=100, B=101, C=110, D=111
```

### 6.2 LZ77 Sliding Window

```
Text: "ABABABAB"
Window: [____] Look-ahead: [ABAB]

Step 1: Window: [A___] Buffer: [BABA]
        Output: (0, 0, 'A')

Step 2: Window: [AB__] Buffer: [ABAB]
        Output: (0, 0, 'B')

Step 3: Window: [ABAB] Buffer: [AB__]
        Match at offset 2, length 2
        Output: (2, 2, 'A')
```

### 6.3 BWT Transformation

```
Original: BANANA$

Sorted rotations:       Last column (BWT):
$BANANA  →  A               ↓
A$BANAN  →  N           A N N B $ A A
ANA$BAN  →  N           └────┬────┘
ANANA$B  →  B                │
BANANA$  →  $           BWT output
NA$BANA  →  A
NANA$BA  →  A
```

## 7. Implementation

```python
from typing import Dict, List, Tuple, Optional
from dataclasses import dataclass, field
from collections import Counter
import heapq


@dataclass(order=True)
class HuffmanNode:
    """Node in Huffman tree."""
    freq: int
    char: Optional[str] = field(compare=False)
    left: Optional['HuffmanNode'] = field(default=None, compare=False)
    right: Optional['HuffmanNode'] = field(default=None, compare=False)
    
    def is_leaf(self) -> bool:
        return self.left is None and self.right is None


class HuffmanCoding:
    """
    Huffman coding for lossless compression.
    
    >>> hc = HuffmanCoding()
    >>> encoded, tree = hc.encode("ABRACADABRA")
    >>> hc.decode(encoded, tree)
    'ABRACADABRA'
    """
    
    def build_tree(self, text: str) -> HuffmanNode:
        """Build Huffman tree from text."""
        freq = Counter(text)
        
        # Create leaf nodes
        heap = [HuffmanNode(f, c) for c, f in freq.items()]
        heapq.heapify(heap)
        
        # Build tree
        while len(heap) > 1:
            left = heapq.heappop(heap)
            right = heapq.heappop(heap)
            merged = HuffmanNode(
                left.freq + right.freq, None, left, right
            )
            heapq.heappush(heap, merged)
        
        return heap[0]
    
    def generate_codes(
        self,
        node: HuffmanNode,
        code: str = "",
        codes: Dict[str, str] = None
    ) -> Dict[str, str]:
        """Generate prefix codes from Huffman tree."""
        if codes is None:
            codes = {}
        
        if node.is_leaf():
            codes[node.char] = code or "0"  # Handle single char
            return codes
        
        if node.left:
            self.generate_codes(node.left, code + "0", codes)
        if node.right:
            self.generate_codes(node.right, code + "1", codes)
        
        return codes
    
    def encode(self, text: str) -> Tuple[str, HuffmanNode]:
        """
        Encode text using Huffman coding.
        
        >>> hc = HuffmanCoding()
        >>> encoded, _ = hc.encode("AAAAABBC")
        >>> len(encoded) < len("AAAAABBC") * 8
        True
        """
        if not text:
            return "", None
        
        tree = self.build_tree(text)
        codes = self.generate_codes(tree)
        
        encoded = ''.join(codes[char] for char in text)
        return encoded, tree
    
    def decode(self, encoded: str, tree: HuffmanNode) -> str:
        """
        Decode Huffman encoded string.
        
        >>> hc = HuffmanCoding()
        >>> encoded, tree = hc.encode("HELLO")
        >>> hc.decode(encoded, tree)
        'HELLO'
        """
        if not encoded or not tree:
            return ""
        
        # Handle single character case
        if tree.is_leaf():
            return tree.char * len(encoded)
        
        result = []
        node = tree
        
        for bit in encoded:
            node = node.left if bit == '0' else node.right
            
            if node.is_leaf():
                result.append(node.char)
                node = tree
        
        return ''.join(result)
    
    def compression_ratio(self, original: str, encoded: str) -> float:
        """Calculate compression ratio."""
        original_bits = len(original) * 8
        compressed_bits = len(encoded)
        return original_bits / compressed_bits if compressed_bits else 0


def rle_encode(data: str) -> List[Tuple[str, int]]:
    """
    Run-Length Encoding.
    
    >>> rle_encode("AAABBBCCCC")
    [('A', 3), ('B', 3), ('C', 4)]
    >>> rle_encode("")
    []
    """
    if not data:
        return []
    
    result = []
    current = data[0]
    count = 1
    
    for char in data[1:]:
        if char == current:
            count += 1
        else:
            result.append((current, count))
            current = char
            count = 1
    
    result.append((current, count))
    return result


def rle_decode(encoded: List[Tuple[str, int]]) -> str:
    """
    Decode Run-Length Encoded data.
    
    >>> rle_decode([('A', 3), ('B', 3), ('C', 4)])
    'AAABBBCCCC'
    """
    return ''.join(char * count for char, count in encoded)


class LZ77:
    """
    LZ77 compression algorithm.
    
    Uses sliding window dictionary.
    """
    
    def __init__(self, window_size: int = 255, lookahead_size: int = 15):
        self.window_size = window_size
        self.lookahead_size = lookahead_size
    
    def compress(
        self, 
        text: str
    ) -> List[Tuple[int, int, str]]:
        """
        Compress text using LZ77.
        
        Returns list of (offset, length, next_char) tuples.
        
        >>> lz = LZ77()
        >>> lz.compress("ABABABAB")
        [(0, 0, 'A'), (0, 0, 'B'), (2, 5, '')]
        """
        result = []
        i = 0
        
        while i < len(text):
            # Find longest match in sliding window
            best_offset = 0
            best_length = 0
            
            start = max(0, i - self.window_size)
            
            for j in range(start, i):
                length = 0
                while (i + length < len(text) and 
                       length < self.lookahead_size and
                       text[j + length % (i - j)] == text[i + length]):
                    length += 1
                
                if length > best_length:
                    best_offset = i - j
                    best_length = length
            
            # Get next character
            next_pos = i + best_length
            next_char = text[next_pos] if next_pos < len(text) else ''
            
            result.append((best_offset, best_length, next_char))
            i = next_pos + (1 if next_char else 0)
        
        return result
    
    def decompress(
        self, 
        compressed: List[Tuple[int, int, str]]
    ) -> str:
        """
        Decompress LZ77 data.
        
        >>> lz = LZ77()
        >>> compressed = lz.compress("ABABABAB")
        >>> lz.decompress(compressed)
        'ABABABAB'
        """
        result = []
        
        for offset, length, next_char in compressed:
            # Copy from buffer
            start = len(result) - offset
            for i in range(length):
                result.append(result[start + i % offset])
            
            # Add next character
            if next_char:
                result.append(next_char)
        
        return ''.join(result)


class LZW:
    """
    Lempel-Ziv-Welch compression.
    
    Dictionary-based compression used in GIF, PDF.
    """
    
    def compress(self, text: str) -> List[int]:
        """
        Compress using LZW algorithm.
        
        >>> lzw = LZW()
        >>> lzw.compress("ABABABA")
        [65, 66, 256, 258]
        """
        # Initialize dictionary with single characters
        dict_size = 256
        dictionary = {chr(i): i for i in range(dict_size)}
        
        result = []
        w = ""
        
        for c in text:
            wc = w + c
            if wc in dictionary:
                w = wc
            else:
                result.append(dictionary[w])
                dictionary[wc] = dict_size
                dict_size += 1
                w = c
        
        if w:
            result.append(dictionary[w])
        
        return result
    
    def decompress(self, compressed: List[int]) -> str:
        """
        Decompress LZW data.
        
        >>> lzw = LZW()
        >>> lzw.decompress([65, 66, 256, 258])
        'ABABABA'
        """
        # Initialize dictionary
        dict_size = 256
        dictionary = {i: chr(i) for i in range(dict_size)}
        
        result = []
        w = chr(compressed[0])
        result.append(w)
        
        for k in compressed[1:]:
            if k in dictionary:
                entry = dictionary[k]
            elif k == dict_size:
                entry = w + w[0]
            else:
                raise ValueError(f"Bad compressed k: {k}")
            
            result.append(entry)
            dictionary[dict_size] = w + entry[0]
            dict_size += 1
            w = entry
        
        return ''.join(result)


def bwt_transform(text: str) -> Tuple[str, int]:
    """
    Burrows-Wheeler Transform.
    
    >>> bwt_transform("BANANA")
    ('ANNB$AA', 4)
    """
    text = text + '$'
    n = len(text)
    
    # Create all rotations with original indices
    rotations = [(text[i:] + text[:i], i) for i in range(n)]
    
    # Sort rotations
    rotations.sort(key=lambda x: x[0])
    
    # Get last column and find original position
    last_column = ''.join(r[0][-1] for r in rotations)
    original_idx = next(i for i, r in enumerate(rotations) if r[1] == 0)
    
    return last_column, original_idx


def bwt_inverse(transformed: str, original_idx: int) -> str:
    """
    Inverse Burrows-Wheeler Transform.
    
    >>> bwt_inverse("ANNB$AA", 4)
    'BANANA'
    """
    n = len(transformed)
    
    # Build table by repeated sorting
    table = [''] * n
    for _ in range(n):
        table = sorted(transformed[i] + table[i] for i in range(n))
    
    # Find row starting with original
    result = table[original_idx]
    return result.rstrip('$')


def move_to_front_encode(text: str) -> List[int]:
    """
    Move-to-Front encoding.
    
    Often used after BWT for better compression.
    
    >>> move_to_front_encode("BANANA")
    [1, 1, 13, 1, 1, 1]
    """
    alphabet = list("$ABCDEFGHIJKLMNOPQRSTUVWXYZ")
    result = []
    
    for char in text:
        idx = alphabet.index(char)
        result.append(idx)
        # Move to front
        alphabet.insert(0, alphabet.pop(idx))
    
    return result


def move_to_front_decode(encoded: List[int]) -> str:
    """
    Decode Move-to-Front.
    
    >>> move_to_front_decode([1, 1, 13, 1, 1, 1])
    'BANANA'
    """
    alphabet = list("$ABCDEFGHIJKLMNOPQRSTUVWXYZ")
    result = []
    
    for idx in encoded:
        char = alphabet[idx]
        result.append(char)
        # Move to front
        alphabet.insert(0, alphabet.pop(idx))
    
    return ''.join(result)


def calculate_entropy(text: str) -> float:
    """
    Calculate Shannon entropy.
    
    >>> round(calculate_entropy("AAAA"), 2)
    0.0
    >>> round(calculate_entropy("ABAB"), 2)
    1.0
    """
    import math
    
    freq = Counter(text)
    n = len(text)
    
    entropy = 0
    for count in freq.values():
        p = count / n
        if p > 0:
            entropy -= p * math.log2(p)
    
    return entropy


# Demo
if __name__ == "__main__":
    print("Compression Algorithms Demo")
    print("=" * 50)
    
    text = "ABRACADABRA"
    
    # Huffman
    print("\n1. Huffman Coding:")
    hc = HuffmanCoding()
    encoded, tree = hc.encode(text)
    print(f"   Original: {text} ({len(text) * 8} bits)")
    print(f"   Encoded:  {encoded} ({len(encoded)} bits)")
    print(f"   Decoded:  {hc.decode(encoded, tree)}")
    print(f"   Ratio:    {hc.compression_ratio(text, encoded):.2f}:1")
    
    # RLE
    print("\n2. Run-Length Encoding:")
    rle_text = "AAABBBCCCC"
    encoded_rle = rle_encode(rle_text)
    print(f"   Original: {rle_text}")
    print(f"   Encoded:  {encoded_rle}")
    print(f"   Decoded:  {rle_decode(encoded_rle)}")
    
    # LZ77
    print("\n3. LZ77:")
    lz = LZ77()
    compressed = lz.compress("ABABABAB")
    print(f"   Original:   'ABABABAB'")
    print(f"   Compressed: {compressed}")
    print(f"   Decoded:    {lz.decompress(compressed)}")
    
    # LZW
    print("\n4. LZW:")
    lzw = LZW()
    compressed_lzw = lzw.compress("ABABABA")
    print(f"   Original:   'ABABABA'")
    print(f"   Compressed: {compressed_lzw}")
    print(f"   Decoded:    {lzw.decompress(compressed_lzw)}")
    
    # BWT
    print("\n5. Burrows-Wheeler Transform:")
    bwt_text = "BANANA"
    transformed, idx = bwt_transform(bwt_text)
    print(f"   Original:    {bwt_text}")
    print(f"   Transformed: {transformed} (idx={idx})")
    print(f"   Inverse:     {bwt_inverse(transformed, idx)}")
    
    # Entropy
    print("\n6. Shannon Entropy:")
    print(f"   'AAAA':       {calculate_entropy('AAAA'):.3f} bits")
    print(f"   'ABAB':       {calculate_entropy('ABAB'):.3f} bits")
    print(f"   'ABCDEFGH':   {calculate_entropy('ABCDEFGH'):.3f} bits")
```

## 8. Applications

### 8.1 File Formats

| Format | Algorithm | Use Case |
|--------|-----------|----------|
| ZIP | DEFLATE (LZ77 + Huffman) | General files |
| GZIP | DEFLATE | Web, Unix |
| PNG | DEFLATE | Images |
| GIF | LZW | Graphics |
| BZIP2 | BWT + MTF + Huffman | Text files |

### 8.2 Network Protocols

- **HTTP compression**: gzip, deflate
- **WebSocket**: per-message compression
- **Database**: column compression

## 9. Real-World Software Engineering Applications

### 9.1 Production Example: Log Compression System

```python
from typing import Dict, List, Optional, BinaryIO
from dataclasses import dataclass
from datetime import datetime
from enum import Enum
import io
import struct
import json


class CompressionMethod(Enum):
    NONE = 0
    RLE = 1
    HUFFMAN = 2
    LZ77 = 3
    LZW = 4
    COMBINED = 5  # BWT + MTF + RLE + Huffman


@dataclass
class CompressionStats:
    """Compression statistics."""
    original_size: int
    compressed_size: int
    compression_time_ms: float
    method: CompressionMethod
    
    @property
    def ratio(self) -> float:
        return self.original_size / max(1, self.compressed_size)
    
    @property
    def savings_percent(self) -> float:
        return (1 - self.compressed_size / max(1, self.original_size)) * 100


class LogCompressor:
    """
    Specialized log file compressor.
    
    Uses pattern-aware compression for structured logs.
    """
    
    def __init__(self):
        self.huffman = HuffmanCoding()
        self.lz77 = LZ77(window_size=4096)
        self.lzw = LZW()
        self._stats: List[CompressionStats] = []
    
    def compress(
        self,
        data: str,
        method: CompressionMethod = CompressionMethod.LZ77
    ) -> bytes:
        """
        Compress log data.
        
        Args:
            data: Log content
            method: Compression algorithm
        
        Returns:
            Compressed bytes
        """
        import time
        start = time.time()
        
        if method == CompressionMethod.NONE:
            result = data.encode('utf-8')
            
        elif method == CompressionMethod.RLE:
            encoded = rle_encode(data)
            # Serialize: count as varint, char as byte
            buffer = io.BytesIO()
            for char, count in encoded:
                buffer.write(self._encode_varint(count))
                buffer.write(char.encode('utf-8'))
            result = buffer.getvalue()
            
        elif method == CompressionMethod.HUFFMAN:
            encoded, tree = self.huffman.encode(data)
            # Serialize tree and bitstream
            result = self._serialize_huffman(encoded, tree)
            
        elif method == CompressionMethod.LZ77:
            compressed = self.lz77.compress(data)
            # Serialize tuples
            result = self._serialize_lz77(compressed)
            
        elif method == CompressionMethod.LZW:
            codes = self.lzw.compress(data)
            # Serialize code list
            buffer = io.BytesIO()
            for code in codes:
                buffer.write(struct.pack('>H', code))
            result = buffer.getvalue()
            
        elif method == CompressionMethod.COMBINED:
            # BWT + MTF + RLE + Huffman pipeline
            bwt_result, idx = bwt_transform(data)
            mtf_result = move_to_front_encode(bwt_result)
            # RLE on MTF output
            rle_result = self._rle_numbers(mtf_result)
            # Huffman on RLE output
            rle_str = ''.join(chr(x) if x < 256 else f'\\x{x:04x}' 
                             for x in rle_result)
            encoded, tree = self.huffman.encode(rle_str)
            result = struct.pack('>I', idx) + self._serialize_huffman(encoded, tree)
        
        else:
            raise ValueError(f"Unknown method: {method}")
        
        elapsed = (time.time() - start) * 1000
        
        self._stats.append(CompressionStats(
            original_size=len(data.encode('utf-8')),
            compressed_size=len(result),
            compression_time_ms=elapsed,
            method=method
        ))
        
        return result
    
    def _encode_varint(self, value: int) -> bytes:
        """Encode integer as variable-length bytes."""
        result = []
        while value > 127:
            result.append((value & 0x7f) | 0x80)
            value >>= 7
        result.append(value)
        return bytes(result)
    
    def _serialize_lz77(
        self,
        compressed: List[Tuple[int, int, str]]
    ) -> bytes:
        """Serialize LZ77 output."""
        buffer = io.BytesIO()
        buffer.write(struct.pack('>I', len(compressed)))
        
        for offset, length, char in compressed:
            buffer.write(struct.pack('>HB', offset, length))
            buffer.write(char.encode('utf-8') if char else b'\x00')
        
        return buffer.getvalue()
    
    def _serialize_huffman(
        self,
        encoded: str,
        tree: HuffmanNode
    ) -> bytes:
        """Serialize Huffman tree and bitstream."""
        # Convert bit string to bytes
        bit_length = len(encoded)
        padded = encoded + '0' * (8 - len(encoded) % 8)
        
        bit_bytes = bytes(
            int(padded[i:i+8], 2) 
            for i in range(0, len(padded), 8)
        )
        
        # Serialize tree (simplified)
        tree_data = self._serialize_tree(tree)
        
        buffer = io.BytesIO()
        buffer.write(struct.pack('>I', bit_length))
        buffer.write(struct.pack('>I', len(tree_data)))
        buffer.write(tree_data)
        buffer.write(bit_bytes)
        
        return buffer.getvalue()
    
    def _serialize_tree(self, node: HuffmanNode) -> bytes:
        """Serialize Huffman tree to bytes."""
        if node.is_leaf():
            return b'\x01' + node.char.encode('utf-8')
        else:
            left = self._serialize_tree(node.left)
            right = self._serialize_tree(node.right)
            return b'\x00' + struct.pack('>I', len(left)) + left + right
    
    def _rle_numbers(self, numbers: List[int]) -> List[int]:
        """RLE for number sequences."""
        if not numbers:
            return []
        
        result = []
        current = numbers[0]
        count = 1
        
        for num in numbers[1:]:
            if num == current:
                count += 1
            else:
                result.extend([current, count])
                current = num
                count = 1
        
        result.extend([current, count])
        return result
    
    def get_stats(self) -> List[CompressionStats]:
        """Get compression statistics."""
        return self._stats
    
    def select_best_method(self, sample: str) -> CompressionMethod:
        """
        Select best compression method based on sample.
        
        Analyzes data characteristics to choose algorithm.
        """
        # Check for runs (RLE-friendly)
        max_run = 1
        current_run = 1
        for i in range(1, len(sample)):
            if sample[i] == sample[i-1]:
                current_run += 1
                max_run = max(max_run, current_run)
            else:
                current_run = 1
        
        if max_run > 10:
            return CompressionMethod.RLE
        
        # Check for repetitive patterns (LZ-friendly)
        unique_ratio = len(set(sample)) / len(sample)
        if unique_ratio < 0.1:
            return CompressionMethod.LZW
        
        # Check entropy
        entropy = calculate_entropy(sample)
        if entropy < 2.0:
            return CompressionMethod.HUFFMAN
        
        # Default to LZ77 for general text
        return CompressionMethod.LZ77


class StreamingCompressor:
    """
    Streaming compression for real-time log processing.
    
    Compresses data in chunks as it arrives.
    """
    
    def __init__(
        self,
        chunk_size: int = 4096,
        method: CompressionMethod = CompressionMethod.LZ77
    ):
        self.chunk_size = chunk_size
        self.method = method
        self._buffer = ""
        self._compressor = LogCompressor()
        self._chunks: List[bytes] = []
    
    def write(self, data: str):
        """Write data to compressor."""
        self._buffer += data
        
        while len(self._buffer) >= self.chunk_size:
            chunk = self._buffer[:self.chunk_size]
            self._buffer = self._buffer[self.chunk_size:]
            self._chunks.append(
                self._compressor.compress(chunk, self.method)
            )
    
    def flush(self) -> bytes:
        """Flush remaining data and return all compressed."""
        if self._buffer:
            self._chunks.append(
                self._compressor.compress(self._buffer, self.method)
            )
            self._buffer = ""
        
        # Combine chunks with length prefixes
        result = io.BytesIO()
        result.write(struct.pack('>I', len(self._chunks)))
        
        for chunk in self._chunks:
            result.write(struct.pack('>I', len(chunk)))
            result.write(chunk)
        
        return result.getvalue()


class CompressionBenchmark:
    """
    Benchmark different compression algorithms.
    """
    
    def __init__(self):
        self.results: Dict[str, List[CompressionStats]] = {}
    
    def run(
        self,
        data: str,
        methods: List[CompressionMethod] = None
    ) -> Dict[CompressionMethod, CompressionStats]:
        """Run benchmark on all methods."""
        if methods is None:
            methods = list(CompressionMethod)
        
        compressor = LogCompressor()
        results = {}
        
        for method in methods:
            try:
                compressor._stats = []
                compressor.compress(data, method)
                results[method] = compressor.get_stats()[0]
            except Exception as e:
                print(f"Error with {method}: {e}")
        
        return results
    
    def format_results(
        self,
        results: Dict[CompressionMethod, CompressionStats]
    ) -> str:
        """Format benchmark results as table."""
        lines = [
            "| Method | Ratio | Savings | Time (ms) |",
            "|--------|-------|---------|-----------|"
        ]
        
        for method, stats in sorted(
            results.items(), 
            key=lambda x: -x[1].ratio
        ):
            lines.append(
                f"| {method.name:10} | {stats.ratio:5.2f}x | "
                f"{stats.savings_percent:5.1f}% | {stats.compression_time_ms:8.2f} |"
            )
        
        return '\n'.join(lines)


# Demo
if __name__ == "__main__":
    print("Log Compression System Demo")
    print("=" * 50)
    
    # Sample log data
    log_data = """
2024-01-15 10:23:45 INFO  [main] Application started
2024-01-15 10:23:46 INFO  [main] Loading configuration
2024-01-15 10:23:46 INFO  [main] Configuration loaded
2024-01-15 10:23:47 DEBUG [worker-1] Processing request
2024-01-15 10:23:47 DEBUG [worker-1] Request completed
2024-01-15 10:23:48 DEBUG [worker-2] Processing request
2024-01-15 10:23:48 DEBUG [worker-2] Request completed
""" * 10  # Repeat for size
    
    # Compress with different methods
    compressor = LogCompressor()
    
    print("\n1. Compression Results:")
    for method in [CompressionMethod.RLE, 
                   CompressionMethod.HUFFMAN,
                   CompressionMethod.LZ77, 
                   CompressionMethod.LZW]:
        compressed = compressor.compress(log_data, method)
        stats = compressor.get_stats()[-1]
        print(f"   {method.name}: {stats.ratio:.2f}x ({stats.savings_percent:.1f}% saved)")
    
    # Streaming compression
    print("\n2. Streaming Compression:")
    streamer = StreamingCompressor(chunk_size=100)
    for line in log_data.split('\n'):
        streamer.write(line + '\n')
    compressed = streamer.flush()
    print(f"   Original: {len(log_data)} bytes")
    print(f"   Compressed: {len(compressed)} bytes")
    
    # Benchmark
    print("\n3. Benchmark:")
    benchmark = CompressionBenchmark()
    results = benchmark.run(log_data)
    print(benchmark.format_results(results))
    
    # Auto-select method
    print("\n4. Auto-select Method:")
    print(f"   Best for log data: {compressor.select_best_method(log_data[:500]).name}")
    print(f"   Best for 'AAAAAAA': {compressor.select_best_method('AAAAAAA').name}")
```

## 10. Comparison

| Algorithm | Best For | Ratio | Speed |
|-----------|----------|-------|-------|
| RLE | Runs | 2-10x | Very Fast |
| Huffman | Low entropy | 1.5-3x | Fast |
| LZ77 | Repetitive | 2-5x | Medium |
| LZW | Text/Code | 2-4x | Fast |
| BWT+MTF | Text | 3-6x | Slow |
| DEFLATE | General | 2-5x | Medium |

## 11. Best Practices

1. **Choose algorithm based on data type**
2. **Consider compression/decompression speed**
3. **Use streaming for large files**
4. **Benchmark on representative data**
5. **Combine transforms for better ratios**

## 12. References

- [Wikipedia: Data Compression](https://en.wikipedia.org/wiki/Data_compression)
- [Huffman Coding](https://en.wikipedia.org/wiki/Huffman_coding)
- [LZ77 and LZ78](https://en.wikipedia.org/wiki/LZ77_and_LZ78)
- Salomon, D. "Data Compression: The Complete Reference"
