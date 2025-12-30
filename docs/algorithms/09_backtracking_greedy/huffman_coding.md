# Huffman Coding

## Overview
- **Category**: Greedy Algorithm / Data Compression
- **Complexity**: Time: O(n log n) | Space: O(n)
- **Type**: Optimal prefix-free encoding
- **Source Files**: [data_compression/huffman.py](../../../data_compression/huffman.py)

## 1. Mathematical Foundation

### 1.1 Prefix-Free Codes

A code is **prefix-free** (or instantaneously decodable) if no codeword is a prefix of another.

**Example**:
- `{0, 10, 110, 111}` is prefix-free
- `{0, 01, 10}` is NOT (0 is prefix of 01)

### 1.2 Average Code Length

For symbols with probabilities $p_1, p_2, ..., p_n$ and code lengths $l_1, l_2, ..., l_n$:

$$
L = \sum_{i=1}^{n} p_i \cdot l_i
$$

### 1.3 Entropy (Theoretical Lower Bound)

$$
H(X) = -\sum_{i=1}^{n} p_i \log_2 p_i
$$

**Shannon's theorem**: $H(X) \leq L < H(X) + 1$

Huffman coding achieves the minimum $L$ for symbol-by-symbol encoding.

### 1.4 Huffman's Greedy Choice

At each step, merge the two symbols with **lowest** frequencies.

**Why it works**: Assigning longer codes to less frequent symbols minimizes average length.

### 1.5 Kraft Inequality

For a prefix-free code with lengths $l_1, ..., l_n$:

$$
\sum_{i=1}^{n} 2^{-l_i} \leq 1
$$

Huffman coding achieves equality when frequencies are powers of 2.

### 1.6 Compression Ratio

$$
\text{Ratio} = \frac{\text{Original size}}{\text{Compressed size}}
$$

$$
\text{Space savings} = 1 - \frac{1}{\text{Ratio}}
$$

## 2. Algorithm Components

### 2.1 Huffman Tree

- **Leaf nodes**: Symbols with their frequencies
- **Internal nodes**: Sum of children's frequencies
- **Left edge**: 0
- **Right edge**: 1
- **Code**: Path from root to leaf

### 2.2 Priority Queue

Use a min-heap to efficiently get the two lowest frequency nodes.

## 3. Pseudocode

### 3.1 Building Huffman Tree

```
ALGORITHM BuildHuffmanTree(symbols, frequencies)
    INPUT: List of symbols and their frequencies
    OUTPUT: Root of Huffman tree
    
    // Create leaf nodes
    Q ← MinPriorityQueue()
    
    for i ← 1 to n do
        node ← CreateLeafNode(symbols[i], frequencies[i])
        Q.insert(node)
    
    // Merge nodes until one remains
    while |Q| > 1 do
        left ← Q.extractMin()
        right ← Q.extractMin()
        
        // Create internal node
        merged ← CreateInternalNode(
            frequency = left.freq + right.freq,
            left = left,
            right = right
        )
        
        Q.insert(merged)
    
    return Q.extractMin()  // Root node
```

### 3.2 Generate Codes

```
ALGORITHM GenerateCodes(root)
    INPUT: Root of Huffman tree
    OUTPUT: Dictionary mapping symbols to codes
    
    codes ← {}
    
    FUNCTION Traverse(node, code)
        if node is leaf then
            codes[node.symbol] ← code
            return
        
        Traverse(node.left, code + "0")
        Traverse(node.right, code + "1")
    
    Traverse(root, "")
    return codes
```

### 3.3 Encoding

```
ALGORITHM Encode(text, codes)
    INPUT: Text to encode, code dictionary
    OUTPUT: Encoded bit string
    
    encoded ← ""
    
    for each symbol in text do
        encoded ← encoded + codes[symbol]
    
    return encoded
```

### 3.4 Decoding

```
ALGORITHM Decode(bitstring, root)
    INPUT: Encoded bit string, Huffman tree root
    OUTPUT: Decoded text
    
    decoded ← ""
    current ← root
    
    for each bit in bitstring do
        if bit = '0' then
            current ← current.left
        else
            current ← current.right
        
        if current is leaf then
            decoded ← decoded + current.symbol
            current ← root  // Reset to root
    
    return decoded
```

## 4. Step-by-Step Example

### Example: Encode "ABRACADABRA"

**Step 1: Count frequencies**
| Symbol | Frequency |
|--------|-----------|
| A | 5 |
| B | 2 |
| R | 2 |
| C | 1 |
| D | 1 |

**Step 2: Build tree**

```
Initial queue: [C:1, D:1, B:2, R:2, A:5]

Merge C(1) and D(1) → CD(2)
Queue: [B:2, R:2, CD:2, A:5]

Merge B(2) and R(2) → BR(4)
Queue: [CD:2, BR:4, A:5]

Merge CD(2) and BR(4) → CDBR(6)
Queue: [A:5, CDBR:6]

Merge A(5) and CDBR(6) → Root(11)
Queue: [Root:11]
```

**Resulting Tree:**
```
           [11]
          /    \
        A(5)   [6]
              /    \
           [2]     [4]
          /   \   /   \
        C(1) D(1) B(2) R(2)
```

**Step 3: Assign codes**
| Symbol | Code |
|--------|------|
| A | 0 |
| C | 100 |
| D | 101 |
| B | 110 |
| R | 111 |

**Step 4: Encode "ABRACADABRA"**
```
A   B   R   A   C   A   D   A   B   R   A
0   110 111 0   100 0   101 0   110 111 0
```
Encoded: `0110111010001010110110`

**Comparison:**
- Original: 11 characters × 8 bits = 88 bits
- Huffman: 23 bits
- Compression ratio: 88/23 ≈ 3.8

## 5. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Build tree | O(n log n) | O(n) |
| Generate codes | O(n) | O(n) |
| Encode | O(m) | O(m) |
| Decode | O(m) | O(n) |

Where n = unique symbols, m = message length.

## 6. Visual Representation

### 6.1 Tree Construction

```
Step 1: Initial leaves
[A:5] [B:2] [R:2] [C:1] [D:1]

Step 2: Merge C and D
[A:5] [B:2] [R:2] [CD:2]
                   /  \
                 C:1  D:1

Step 3: Merge B and R
[A:5] [BR:4] [CD:2]
       /  \
     B:2  R:2

Step 4: Merge CD and BR
[A:5]     [CDBR:6]
          /      \
       [2]        [4]
       / \        / \
      C   D      B   R

Step 5: Merge A and CDBR
          [11]
         /    \
       A:5   [6]
            /    \
          [2]    [4]
         /  \   /   \
        C    D B     R
```

### 6.2 Code Assignment

```
          [Root]
         /      \
        0        1
       /          \
      A           [.]
                 /    \
                0      1
               /        \
             [.]        [.]
            /  \       /   \
           0    1     0     1
           |    |     |     |
           C    D     B     R

Codes: A=0, C=100, D=101, B=110, R=111
```

## 7. Implementation

```python
from typing import Dict, List, Optional, Tuple
from dataclasses import dataclass, field
from collections import Counter
import heapq


@dataclass(order=True)
class HuffmanNode:
    """Node in Huffman tree."""
    freq: int
    symbol: Optional[str] = field(compare=False, default=None)
    left: Optional['HuffmanNode'] = field(compare=False, default=None)
    right: Optional['HuffmanNode'] = field(compare=False, default=None)
    
    def is_leaf(self) -> bool:
        return self.left is None and self.right is None


class HuffmanCoding:
    """
    Huffman coding for text compression.
    
    Implements optimal prefix-free encoding using greedy algorithm.
    """
    
    def __init__(self):
        self.root: Optional[HuffmanNode] = None
        self.codes: Dict[str, str] = {}
        self.reverse_codes: Dict[str, str] = {}
    
    def build_tree(self, text: str) -> HuffmanNode:
        """
        Build Huffman tree from text.
        
        >>> hc = HuffmanCoding()
        >>> root = hc.build_tree("ABRACADABRA")
        >>> root.freq
        11
        """
        # Count frequencies
        freq = Counter(text)
        
        # Create priority queue of leaf nodes
        heap: List[HuffmanNode] = []
        for symbol, count in freq.items():
            heapq.heappush(heap, HuffmanNode(count, symbol))
        
        # Handle single character case
        if len(heap) == 1:
            node = heapq.heappop(heap)
            self.root = HuffmanNode(node.freq, left=node)
            self._generate_codes(self.root, "")
            return self.root
        
        # Build tree by merging
        while len(heap) > 1:
            left = heapq.heappop(heap)
            right = heapq.heappop(heap)
            
            merged = HuffmanNode(
                freq=left.freq + right.freq,
                left=left,
                right=right
            )
            heapq.heappush(heap, merged)
        
        self.root = heap[0]
        self._generate_codes(self.root, "")
        
        return self.root
    
    def _generate_codes(self, node: HuffmanNode, code: str):
        """Generate codes by traversing tree."""
        if node.is_leaf():
            # Handle empty code (single character)
            self.codes[node.symbol] = code if code else "0"
            self.reverse_codes[code if code else "0"] = node.symbol
            return
        
        if node.left:
            self._generate_codes(node.left, code + "0")
        if node.right:
            self._generate_codes(node.right, code + "1")
    
    def encode(self, text: str) -> str:
        """
        Encode text using Huffman codes.
        
        >>> hc = HuffmanCoding()
        >>> hc.build_tree("ABRACADABRA")
        <...>
        >>> encoded = hc.encode("ABRACADABRA")
        >>> len(encoded) < len("ABRACADABRA") * 8
        True
        """
        if not self.codes:
            self.build_tree(text)
        
        return ''.join(self.codes[char] for char in text)
    
    def decode(self, encoded: str) -> str:
        """
        Decode Huffman-encoded bit string.
        
        >>> hc = HuffmanCoding()
        >>> hc.build_tree("ABRACADABRA")
        <...>
        >>> encoded = hc.encode("ABRACADABRA")
        >>> hc.decode(encoded)
        'ABRACADABRA'
        """
        if self.root is None:
            return ""
        
        decoded = []
        current = self.root
        
        for bit in encoded:
            if bit == '0':
                current = current.left
            else:
                current = current.right
            
            if current.is_leaf():
                decoded.append(current.symbol)
                current = self.root
        
        return ''.join(decoded)
    
    def get_compression_stats(self, text: str) -> Dict:
        """
        Get compression statistics.
        
        >>> hc = HuffmanCoding()
        >>> hc.build_tree("ABRACADABRA")
        <...>
        >>> stats = hc.get_compression_stats("ABRACADABRA")
        >>> stats['compression_ratio'] > 1
        True
        """
        encoded = self.encode(text)
        
        original_bits = len(text) * 8
        compressed_bits = len(encoded)
        
        # Calculate entropy
        freq = Counter(text)
        n = len(text)
        entropy = -sum(
            (f / n) * (f / n).bit_length() if f > 0 else 0
            for f in freq.values()
        )
        
        return {
            'original_size': len(text),
            'original_bits': original_bits,
            'compressed_bits': compressed_bits,
            'compression_ratio': original_bits / compressed_bits,
            'space_savings': 1 - compressed_bits / original_bits,
            'average_code_length': compressed_bits / len(text),
            'codes': self.codes.copy()
        }
    
    def print_codes(self):
        """Print the code table."""
        print("\nHuffman Codes:")
        print("-" * 30)
        for symbol, code in sorted(self.codes.items()):
            display = repr(symbol) if symbol in '\n\t ' else symbol
            print(f"{display}: {code}")


class HuffmanWithFrequencies:
    """
    Huffman coding when frequencies are pre-computed.
    """
    
    def __init__(self, frequencies: Dict[str, int]):
        """
        Initialize with known frequencies.
        
        >>> hc = HuffmanWithFrequencies({'A': 5, 'B': 2, 'R': 2, 'C': 1, 'D': 1})
        >>> hc.codes['A']
        '0'
        """
        self.frequencies = frequencies
        self.root = None
        self.codes: Dict[str, str] = {}
        
        self._build_tree()
    
    def _build_tree(self):
        """Build Huffman tree from frequencies."""
        heap = [HuffmanNode(freq, symbol) 
                for symbol, freq in self.frequencies.items()]
        heapq.heapify(heap)
        
        if len(heap) == 1:
            node = heapq.heappop(heap)
            self.root = HuffmanNode(node.freq, left=node)
        else:
            while len(heap) > 1:
                left = heapq.heappop(heap)
                right = heapq.heappop(heap)
                merged = HuffmanNode(left.freq + right.freq, left=left, right=right)
                heapq.heappush(heap, merged)
            
            self.root = heap[0]
        
        self._generate_codes(self.root, "")
    
    def _generate_codes(self, node: HuffmanNode, code: str):
        if node.is_leaf():
            self.codes[node.symbol] = code if code else "0"
            return
        
        if node.left:
            self._generate_codes(node.left, code + "0")
        if node.right:
            self._generate_codes(node.right, code + "1")


class AdaptiveHuffman:
    """
    Adaptive (Dynamic) Huffman coding.
    
    Updates tree as symbols are processed - no need for
    separate frequency counting pass.
    """
    
    def __init__(self, alphabet_size: int = 256):
        self.alphabet_size = alphabet_size
        self.nyt_node = None  # Not Yet Transmitted node
        self.root = None
        self.nodes: Dict[str, HuffmanNode] = {}
        self._initialize()
    
    def _initialize(self):
        """Initialize with NYT node."""
        self.nyt_node = HuffmanNode(freq=0, symbol="NYT")
        self.root = self.nyt_node
        self.nodes = {}
    
    def encode_symbol(self, symbol: str) -> str:
        """Encode a single symbol and update tree."""
        if symbol in self.nodes:
            # Symbol seen before - output its code
            code = self._get_code(self.nodes[symbol])
        else:
            # New symbol - output NYT code + fixed code for symbol
            nyt_code = self._get_code(self.nyt_node)
            fixed_code = format(ord(symbol), '08b')
            code = nyt_code + fixed_code
        
        # Update tree
        self._update_tree(symbol)
        
        return code
    
    def _get_code(self, target: HuffmanNode) -> str:
        """Get code for a node by traversing from root."""
        code = []
        current = target
        
        # Find path from node to root
        while current != self.root:
            parent = self._find_parent(current)
            if parent is None:
                break
            if parent.left == current:
                code.append('0')
            else:
                code.append('1')
            current = parent
        
        return ''.join(reversed(code))
    
    def _find_parent(self, node: HuffmanNode) -> Optional[HuffmanNode]:
        """Find parent of a node (BFS)."""
        from collections import deque
        
        if self.root == node:
            return None
        
        queue = deque([self.root])
        while queue:
            current = queue.popleft()
            if current.left == node or current.right == node:
                return current
            if current.left:
                queue.append(current.left)
            if current.right:
                queue.append(current.right)
        
        return None
    
    def _update_tree(self, symbol: str):
        """Update tree after processing symbol."""
        if symbol not in self.nodes:
            # Create new node
            new_node = HuffmanNode(freq=1, symbol=symbol)
            new_internal = HuffmanNode(
                freq=1,
                left=self.nyt_node,
                right=new_node
            )
            
            if self.root == self.nyt_node:
                self.root = new_internal
            else:
                parent = self._find_parent(self.nyt_node)
                if parent:
                    if parent.left == self.nyt_node:
                        parent.left = new_internal
                    else:
                        parent.right = new_internal
            
            self.nodes[symbol] = new_node
        else:
            # Increment frequency and potentially swap
            self.nodes[symbol].freq += 1
        
        # Update frequencies up to root
        self._update_frequencies()
    
    def _update_frequencies(self):
        """Update internal node frequencies."""
        def update(node):
            if node is None or node.is_leaf():
                return node.freq if node else 0
            
            left_freq = update(node.left)
            right_freq = update(node.right)
            node.freq = left_freq + right_freq
            return node.freq
        
        update(self.root)


def huffman_encode(text: str) -> Tuple[str, Dict[str, str]]:
    """
    Convenience function for Huffman encoding.
    
    >>> encoded, codes = huffman_encode("ABRACADABRA")
    >>> len(encoded) < len("ABRACADABRA") * 8
    True
    """
    hc = HuffmanCoding()
    hc.build_tree(text)
    encoded = hc.encode(text)
    return encoded, hc.codes


def huffman_decode(encoded: str, codes: Dict[str, str]) -> str:
    """
    Convenience function for Huffman decoding.
    
    >>> encoded, codes = huffman_encode("HELLO")
    >>> huffman_decode(encoded, codes)
    'HELLO'
    """
    # Build reverse lookup
    reverse = {v: k for k, v in codes.items()}
    
    decoded = []
    current = ""
    
    for bit in encoded:
        current += bit
        if current in reverse:
            decoded.append(reverse[current])
            current = ""
    
    return ''.join(decoded)
```

## 8. Applications

### 8.1 Common Use Cases

- **File compression**: ZIP, GZIP, DEFLATE
- **Image compression**: JPEG (DC coefficients)
- **Video codecs**: Part of H.264, H.265
- **Network protocols**: HTTP/2 HPACK headers
- **Fax machines**: Modified Huffman coding

### 8.2 Variations

| Variant | Description |
|---------|-------------|
| Canonical Huffman | Standardized code assignment |
| Adaptive Huffman | Dynamic tree updates |
| Extended Huffman | Multiple symbols per codeword |
| Length-Limited | Maximum code length constraint |

## 9. Real-World Software Engineering Applications

### 9.1 Production Example: Log Compression System

```python
from typing import Dict, List, Tuple, Optional, BinaryIO
from dataclasses import dataclass
from collections import Counter
import struct
import json
from datetime import datetime


@dataclass
class CompressionHeader:
    """Header for compressed file."""
    magic: bytes = b'HUFF'
    version: int = 1
    original_size: int = 0
    compressed_size: int = 0
    checksum: int = 0


class LogCompressor:
    """
    Production Huffman compressor for log files.
    
    Optimized for typical log file patterns.
    """
    
    def __init__(self):
        self.huffman = HuffmanCoding()
        
        # Pre-computed codes for common log patterns
        self.common_patterns = [
            'INFO', 'DEBUG', 'WARN', 'ERROR', 'FATAL',
            '2023', '2024', '2025',
            'GET', 'POST', 'PUT', 'DELETE',
            'HTTP', 'HTTPS',
            'null', 'true', 'false',
            'exception', 'Exception',
        ]
    
    def compress_file(
        self,
        input_path: str,
        output_path: str
    ) -> Dict:
        """
        Compress a log file.
        
        Args:
            input_path: Path to input file
            output_path: Path to output file
        
        Returns:
            Compression statistics
        """
        # Read file
        with open(input_path, 'r', encoding='utf-8') as f:
            content = f.read()
        
        # Compress
        compressed, codes = self.compress(content)
        
        # Create header
        header = CompressionHeader(
            original_size=len(content),
            compressed_size=len(compressed) // 8 + 1,
            checksum=self._compute_checksum(content)
        )
        
        # Write compressed file
        with open(output_path, 'wb') as f:
            self._write_header(f, header)
            self._write_codes(f, codes)
            self._write_data(f, compressed)
        
        return {
            'original_size': header.original_size,
            'compressed_size': header.compressed_size + 
                              self._estimate_header_size(codes),
            'ratio': header.original_size / 
                    (header.compressed_size + self._estimate_header_size(codes))
        }
    
    def compress(self, text: str) -> Tuple[str, Dict[str, str]]:
        """Compress text to bit string."""
        self.huffman.build_tree(text)
        encoded = self.huffman.encode(text)
        return encoded, self.huffman.codes
    
    def decompress_file(self, input_path: str, output_path: str):
        """Decompress a file."""
        with open(input_path, 'rb') as f:
            header = self._read_header(f)
            codes = self._read_codes(f)
            compressed = self._read_data(f)
        
        # Decompress
        content = huffman_decode(compressed, codes)
        
        # Verify checksum
        if self._compute_checksum(content) != header.checksum:
            raise ValueError("Checksum mismatch - file corrupted")
        
        with open(output_path, 'w', encoding='utf-8') as f:
            f.write(content)
    
    def _write_header(self, f: BinaryIO, header: CompressionHeader):
        """Write header to file."""
        f.write(header.magic)
        f.write(struct.pack('>I', header.version))
        f.write(struct.pack('>Q', header.original_size))
        f.write(struct.pack('>Q', header.compressed_size))
        f.write(struct.pack('>I', header.checksum))
    
    def _read_header(self, f: BinaryIO) -> CompressionHeader:
        """Read header from file."""
        magic = f.read(4)
        if magic != b'HUFF':
            raise ValueError("Invalid file format")
        
        version = struct.unpack('>I', f.read(4))[0]
        original_size = struct.unpack('>Q', f.read(8))[0]
        compressed_size = struct.unpack('>Q', f.read(8))[0]
        checksum = struct.unpack('>I', f.read(4))[0]
        
        return CompressionHeader(
            magic=magic,
            version=version,
            original_size=original_size,
            compressed_size=compressed_size,
            checksum=checksum
        )
    
    def _write_codes(self, f: BinaryIO, codes: Dict[str, str]):
        """Write code table to file."""
        # Serialize as JSON for simplicity
        codes_json = json.dumps(codes)
        codes_bytes = codes_json.encode('utf-8')
        
        f.write(struct.pack('>I', len(codes_bytes)))
        f.write(codes_bytes)
    
    def _read_codes(self, f: BinaryIO) -> Dict[str, str]:
        """Read code table from file."""
        codes_len = struct.unpack('>I', f.read(4))[0]
        codes_bytes = f.read(codes_len)
        return json.loads(codes_bytes.decode('utf-8'))
    
    def _write_data(self, f: BinaryIO, bitstring: str):
        """Write compressed data as bytes."""
        # Pad to byte boundary
        padding = (8 - len(bitstring) % 8) % 8
        bitstring += '0' * padding
        
        # Write padding info
        f.write(struct.pack('B', padding))
        
        # Convert to bytes
        data = bytes(
            int(bitstring[i:i+8], 2)
            for i in range(0, len(bitstring), 8)
        )
        f.write(data)
    
    def _read_data(self, f: BinaryIO) -> str:
        """Read compressed data as bitstring."""
        padding = struct.unpack('B', f.read(1))[0]
        data = f.read()
        
        # Convert to bitstring
        bitstring = ''.join(format(b, '08b') for b in data)
        
        # Remove padding
        if padding:
            bitstring = bitstring[:-padding]
        
        return bitstring
    
    def _compute_checksum(self, text: str) -> int:
        """Compute simple checksum."""
        return sum(ord(c) for c in text) & 0xFFFFFFFF
    
    def _estimate_header_size(self, codes: Dict[str, str]) -> int:
        """Estimate header size in bytes."""
        return 28 + len(json.dumps(codes))


class StreamingHuffman:
    """
    Streaming Huffman for real-time log compression.
    
    Maintains a sliding window for frequency estimation.
    """
    
    def __init__(self, window_size: int = 10000):
        self.window_size = window_size
        self.window: List[str] = []
        self.freq: Counter = Counter()
        self.huffman: Optional[HuffmanCoding] = None
        self.rebuild_threshold = 1000
        self.chars_since_rebuild = 0
    
    def encode_chunk(self, chunk: str) -> Tuple[str, bool]:
        """
        Encode a chunk of text.
        
        Returns:
            Tuple of (encoded bits, whether codes changed)
        """
        codes_changed = False
        
        # Update window
        for char in chunk:
            self.window.append(char)
            self.freq[char] += 1
            self.chars_since_rebuild += 1
            
            # Trim window
            if len(self.window) > self.window_size:
                removed = self.window.pop(0)
                self.freq[removed] -= 1
                if self.freq[removed] == 0:
                    del self.freq[removed]
        
        # Rebuild tree if needed
        if (self.huffman is None or 
            self.chars_since_rebuild >= self.rebuild_threshold):
            self._rebuild_tree()
            codes_changed = True
        
        # Encode
        encoded = ''.join(self.huffman.codes.get(c, '?') for c in chunk)
        
        return encoded, codes_changed
    
    def _rebuild_tree(self):
        """Rebuild Huffman tree from current frequencies."""
        if not self.freq:
            return
        
        self.huffman = HuffmanWithFrequencies(dict(self.freq))
        self.chars_since_rebuild = 0
    
    def get_current_codes(self) -> Dict[str, str]:
        """Get current code table."""
        if self.huffman:
            return self.huffman.codes.copy()
        return {}


# Demo
if __name__ == "__main__":
    print("Huffman Coding Demo")
    print("=" * 50)
    
    # Basic example
    print("\n1. Basic Huffman Encoding:")
    text = "ABRACADABRA"
    
    hc = HuffmanCoding()
    hc.build_tree(text)
    encoded = hc.encode(text)
    decoded = hc.decode(encoded)
    
    print(f"   Original: {text}")
    print(f"   Encoded:  {encoded}")
    print(f"   Decoded:  {decoded}")
    hc.print_codes()
    
    # Statistics
    print("\n2. Compression Statistics:")
    stats = hc.get_compression_stats(text)
    print(f"   Original bits: {stats['original_bits']}")
    print(f"   Compressed bits: {stats['compressed_bits']}")
    print(f"   Compression ratio: {stats['compression_ratio']:.2f}")
    print(f"   Space savings: {stats['space_savings']:.1%}")
    
    # Larger example
    print("\n3. Larger Text Example:")
    sample_log = """
    2024-01-15 10:30:45 INFO Starting application
    2024-01-15 10:30:46 DEBUG Loading configuration
    2024-01-15 10:30:47 INFO Connected to database
    2024-01-15 10:30:48 WARN High memory usage detected
    2024-01-15 10:30:49 ERROR Connection timeout
    """ * 10
    
    hc2 = HuffmanCoding()
    hc2.build_tree(sample_log)
    stats2 = hc2.get_compression_stats(sample_log)
    
    print(f"   Original size: {stats2['original_size']} chars")
    print(f"   Compression ratio: {stats2['compression_ratio']:.2f}")
    print(f"   Average code length: {stats2['average_code_length']:.2f} bits/char")
    
    # Log compressor
    print("\n4. Log Compressor:")
    compressor = LogCompressor()
    result = compressor.compress(sample_log)
    print(f"   Compressed size ratio: {result[0][:50]}...")
```

## 10. Comparison

| Algorithm | Compression | Speed | Memory | Use Case |
|-----------|-------------|-------|--------|----------|
| Huffman | Good | Fast | Low | General text |
| Arithmetic | Better | Slower | Medium | High compression |
| LZW | Good | Fast | Medium | GIF, Unix compress |
| LZ77 | Very Good | Medium | Medium | DEFLATE, ZIP |

## 11. Best Practices

1. **Use canonical Huffman** for consistent decoding
2. **Include code table** in compressed output
3. **Handle edge cases** (empty input, single char)
4. **Use priority queue** for efficient tree building
5. **Consider block-based** compression for large files
6. **Combine with LZ77** for better compression (DEFLATE)

## 12. References

- [Wikipedia: Huffman coding](https://en.wikipedia.org/wiki/Huffman_coding)
- Huffman, D. "A Method for the Construction of Minimum-Redundancy Codes" (1952)
- Cormen, T. et al. "Introduction to Algorithms" - Chapter 16
