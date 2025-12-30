# Classical Ciphers

## Overview
- **Category**: Cryptography / Historical Ciphers
- **Complexity**: Time: O(n) | Space: O(n)
- **Type**: Substitution, transposition, polyalphabetic
- **Source Files**: [ciphers/](../../../ciphers/)

## 1. Mathematical Foundation

### 1.1 Modular Arithmetic

Core operation for letter substitution:
$$
c = (p + k) \mod 26
$$

Where:
- $c$ = ciphertext position
- $p$ = plaintext position  
- $k$ = key/shift value

### 1.2 Substitution Ciphers

**Caesar Cipher:**
$$
E(x) = (x + k) \mod 26
$$
$$
D(y) = (y - k) \mod 26
$$

**Affine Cipher:**
$$
E(x) = (ax + b) \mod 26
$$
$$
D(y) = a^{-1}(y - b) \mod 26
$$

Where $\gcd(a, 26) = 1$ for invertibility.

### 1.3 Polyalphabetic Ciphers

**Vigenère Cipher:**
$$
C_i = (P_i + K_{i \mod m}) \mod 26
$$

Where $K$ is keyword of length $m$.

### 1.4 Transposition Ciphers

Rearrange character positions using permutation:
$$
\pi: \{0, 1, ..., n-1\} \to \{0, 1, ..., n-1\}
$$

### 1.5 Letter Frequency Analysis

English letter frequencies for cryptanalysis:

| Letter | Frequency |
|--------|-----------|
| E | 12.7% |
| T | 9.1% |
| A | 8.2% |
| O | 7.5% |
| I | 7.0% |

## 2. Cipher Categories

### 2.1 Substitution Ciphers
- **Caesar**: Fixed shift
- **ROT13**: Caesar with k=13
- **Atbash**: Reverse alphabet
- **Affine**: Linear transformation
- **Simple**: Arbitrary mapping

### 2.2 Polyalphabetic Ciphers
- **Vigenère**: Repeating keyword
- **Autokey**: Plaintext as key
- **Beaufort**: Reciprocal Vigenère
- **Running Key**: Book cipher

### 2.3 Transposition Ciphers
- **Rail Fence**: Zigzag writing
- **Columnar**: Column reordering
- **Route Cipher**: Geometric patterns
- **Scytale**: Spiral wrapping

## 3. Pseudocode

### 3.1 Caesar Cipher

```
ALGORITHM CaesarEncrypt(plaintext, shift)
    INPUT: Text string, shift amount
    OUTPUT: Encrypted string
    
    ciphertext ← ""
    for each char in plaintext do
        if char is letter then
            base ← 'A' if uppercase else 'a'
            offset ← (char - base + shift) mod 26
            ciphertext ← ciphertext + (base + offset)
        else
            ciphertext ← ciphertext + char
    return ciphertext
```

### 3.2 Vigenère Cipher

```
ALGORITHM VigenereEncrypt(plaintext, keyword)
    INPUT: Text string, keyword
    OUTPUT: Encrypted string
    
    ciphertext ← ""
    key_index ← 0
    
    for each char in plaintext do
        if char is letter then
            base ← 'A' if uppercase else 'a'
            shift ← keyword[key_index mod length(keyword)] - 'A'
            offset ← (char - base + shift) mod 26
            ciphertext ← ciphertext + (base + offset)
            key_index ← key_index + 1
        else
            ciphertext ← ciphertext + char
    return ciphertext
```

### 3.3 Rail Fence Cipher

```
ALGORITHM RailFenceEncrypt(plaintext, rails)
    INPUT: Text string, number of rails
    OUTPUT: Encrypted string
    
    fence ← 2D array [rails][length(plaintext)]
    rail ← 0
    direction ← 1
    
    for i ← 0 to length(plaintext) - 1 do
        fence[rail][i] ← plaintext[i]
        rail ← rail + direction
        if rail = 0 or rail = rails - 1 then
            direction ← -direction
    
    ciphertext ← ""
    for each row in fence do
        ciphertext ← ciphertext + row contents
    return ciphertext
```

### 3.4 Columnar Transposition

```
ALGORITHM ColumnarEncrypt(plaintext, key)
    INPUT: Text string, key string (defines column order)
    OUTPUT: Encrypted string
    
    columns ← length(key)
    rows ← ceil(length(plaintext) / columns)
    grid ← fill with plaintext, pad if needed
    
    order ← get column order from alphabetical sort of key
    
    ciphertext ← ""
    for each column in sorted order do
        for each row do
            ciphertext ← ciphertext + grid[row][column]
    return ciphertext
```

## 4. Step-by-Step Example

### Example 1: Caesar Cipher (shift=3)

```
Plaintext:  H E L L O  W O R L D
Position:   7 4 11 11 14  22 14 17 11 3
+3:         10 7 14 14 17  25 17 20 14 6
Ciphertext: K H O O R  Z R U O G
```

### Example 2: Vigenère with "KEY"

```
Plaintext:  H E L L O
Key:        K E Y K E
Key shift:  10 4 24 10 4

H(7) + K(10) = R(17)
E(4) + E(4) = I(8)
L(11) + Y(24) = J(9)
L(11) + K(10) = V(21)
O(14) + E(4) = S(18)

Ciphertext: RIJVS
```

### Example 3: Rail Fence (3 rails)

```
Plaintext: HELLO WORLD (as HELLOWORLD)

Rail 0: H . . . O . . . L .
Rail 1: . E . L . W . R . D
Rail 2: . . L . . . O . . .

Read row by row: HOLELOWORDL → HOLEL WORDO
                 Actually: H-O-L + E-L-W-R-D + L-O
                 = HOLELWRDLO
```

## 5. Complexity Analysis

| Cipher | Encrypt Time | Decrypt Time | Key Space |
|--------|--------------|--------------|-----------|
| Caesar | O(n) | O(n) | 26 |
| Affine | O(n) | O(n) | 312 |
| Vigenère | O(n) | O(n) | 26^m |
| Rail Fence | O(n) | O(n) | n-1 |
| Columnar | O(n log k) | O(n log k) | k! |

Where n = text length, m = key length, k = column count

## 6. Visual Representation

### 6.1 Caesar Cipher Shift

```
Plain:  A B C D E F G H I J K L M N O P Q R S T U V W X Y Z
        ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓
Cipher: D E F G H I J K L M N O P Q R S T U V W X Y Z A B C
        ← Shift by 3 →
```

### 6.2 Vigenère Tableau (Partial)

```
    A B C D E F G H I J K L M N O P Q R S T U V W X Y Z
A   A B C D E F G H I J K L M N O P Q R S T U V W X Y Z
B   B C D E F G H I J K L M N O P Q R S T U V W X Y Z A
C   C D E F G H I J K L M N O P Q R S T U V W X Y Z A B
D   D E F G H I J K L M N O P Q R S T U V W X Y Z A B C
...
K   K L M N O P Q R S T U V W X Y Z A B C D E F G H I J
...
```

### 6.3 Rail Fence Pattern

```
Text: WEAREDISCOVERED

3 Rails:
W . . . E . . . C . . . E .
. E . R . D . S . O . E . D
. . A . . . I . . . V . . .

Read: WECEER + EDSOEVD + AIVR → WECEEREDSOEDAIVR
```

## 7. Implementation

```python
from typing import List, Dict, Tuple, Optional
import string


def caesar_encrypt(plaintext: str, shift: int) -> str:
    """
    Encrypt using Caesar cipher.
    
    >>> caesar_encrypt("HELLO", 3)
    'KHOOR'
    >>> caesar_encrypt("Hello, World!", 3)
    'Khoor, Zruog!'
    """
    result = []
    for char in plaintext:
        if char.isalpha():
            base = ord('A') if char.isupper() else ord('a')
            shifted = (ord(char) - base + shift) % 26
            result.append(chr(base + shifted))
        else:
            result.append(char)
    return ''.join(result)


def caesar_decrypt(ciphertext: str, shift: int) -> str:
    """
    Decrypt Caesar cipher.
    
    >>> caesar_decrypt("KHOOR", 3)
    'HELLO'
    """
    return caesar_encrypt(ciphertext, -shift)


def caesar_crack(ciphertext: str) -> List[Tuple[int, str]]:
    """
    Brute force all 26 possible shifts.
    
    >>> caesar_crack("KHOOR")[3]
    (3, 'HELLO')
    """
    return [(shift, caesar_decrypt(ciphertext, shift)) 
            for shift in range(26)]


def rot13(text: str) -> str:
    """
    ROT13 cipher (Caesar with shift 13).
    
    Self-inverse: rot13(rot13(x)) == x
    
    >>> rot13("HELLO")
    'URYYB'
    >>> rot13("URYYB")
    'HELLO'
    """
    return caesar_encrypt(text, 13)


def atbash_cipher(text: str) -> str:
    """
    Atbash cipher - reverse alphabet substitution.
    
    A↔Z, B↔Y, C↔X, etc.
    
    >>> atbash_cipher("HELLO")
    'SVOOL'
    >>> atbash_cipher("SVOOL")
    'HELLO'
    """
    result = []
    for char in text:
        if char.isalpha():
            base = ord('A') if char.isupper() else ord('a')
            reversed_pos = 25 - (ord(char) - base)
            result.append(chr(base + reversed_pos))
        else:
            result.append(char)
    return ''.join(result)


def affine_encrypt(plaintext: str, a: int, b: int) -> str:
    """
    Affine cipher: E(x) = (ax + b) mod 26.
    
    Requires gcd(a, 26) = 1.
    
    >>> affine_encrypt("HELLO", 5, 8)
    'RCLLA'
    """
    from math import gcd
    if gcd(a, 26) != 1:
        raise ValueError(f"a={a} must be coprime with 26")
    
    result = []
    for char in plaintext:
        if char.isalpha():
            base = ord('A') if char.isupper() else ord('a')
            x = ord(char) - base
            encrypted = (a * x + b) % 26
            result.append(chr(base + encrypted))
        else:
            result.append(char)
    return ''.join(result)


def mod_inverse(a: int, m: int) -> int:
    """Find modular multiplicative inverse using extended Euclidean."""
    def extended_gcd(a: int, b: int) -> Tuple[int, int, int]:
        if a == 0:
            return b, 0, 1
        gcd, x1, y1 = extended_gcd(b % a, a)
        return gcd, y1 - (b // a) * x1, x1
    
    gcd, x, _ = extended_gcd(a % m, m)
    if gcd != 1:
        raise ValueError(f"No inverse for {a} mod {m}")
    return (x % m + m) % m


def affine_decrypt(ciphertext: str, a: int, b: int) -> str:
    """
    Decrypt affine cipher.
    
    >>> affine_decrypt("RCLLA", 5, 8)
    'HELLO'
    """
    a_inv = mod_inverse(a, 26)
    
    result = []
    for char in ciphertext:
        if char.isalpha():
            base = ord('A') if char.isupper() else ord('a')
            y = ord(char) - base
            decrypted = (a_inv * (y - b)) % 26
            result.append(chr(base + decrypted))
        else:
            result.append(char)
    return ''.join(result)


def vigenere_encrypt(plaintext: str, keyword: str) -> str:
    """
    Vigenère cipher encryption.
    
    >>> vigenere_encrypt("HELLO", "KEY")
    'RIJVS'
    """
    keyword = keyword.upper()
    result = []
    key_index = 0
    
    for char in plaintext:
        if char.isalpha():
            base = ord('A') if char.isupper() else ord('a')
            shift = ord(keyword[key_index % len(keyword)]) - ord('A')
            encrypted = (ord(char) - base + shift) % 26
            result.append(chr(base + encrypted))
            key_index += 1
        else:
            result.append(char)
    return ''.join(result)


def vigenere_decrypt(ciphertext: str, keyword: str) -> str:
    """
    Vigenère cipher decryption.
    
    >>> vigenere_decrypt("RIJVS", "KEY")
    'HELLO'
    """
    keyword = keyword.upper()
    result = []
    key_index = 0
    
    for char in ciphertext:
        if char.isalpha():
            base = ord('A') if char.isupper() else ord('a')
            shift = ord(keyword[key_index % len(keyword)]) - ord('A')
            decrypted = (ord(char) - base - shift) % 26
            result.append(chr(base + decrypted))
            key_index += 1
        else:
            result.append(char)
    return ''.join(result)


def rail_fence_encrypt(plaintext: str, rails: int) -> str:
    """
    Rail Fence cipher encryption.
    
    >>> rail_fence_encrypt("HELLO", 3)
    'HOELL'
    """
    if rails == 1:
        return plaintext
    
    # Create fence pattern
    fence = [[] for _ in range(rails)]
    rail = 0
    direction = 1
    
    for char in plaintext:
        fence[rail].append(char)
        rail += direction
        if rail == 0 or rail == rails - 1:
            direction = -direction
    
    return ''.join(''.join(row) for row in fence)


def rail_fence_decrypt(ciphertext: str, rails: int) -> str:
    """
    Rail Fence cipher decryption.
    
    >>> rail_fence_decrypt("HOELL", 3)
    'HELLO'
    """
    if rails == 1:
        return ciphertext
    
    n = len(ciphertext)
    
    # Calculate characters per rail
    pattern = []
    rail = 0
    direction = 1
    for i in range(n):
        pattern.append(rail)
        rail += direction
        if rail == 0 or rail == rails - 1:
            direction = -direction
    
    rail_lengths = [pattern.count(r) for r in range(rails)]
    
    # Split ciphertext into rails
    fence = []
    index = 0
    for length in rail_lengths:
        fence.append(list(ciphertext[index:index + length]))
        index += length
    
    # Read in zigzag pattern
    result = []
    rail_indices = [0] * rails
    rail = 0
    direction = 1
    
    for _ in range(n):
        result.append(fence[rail][rail_indices[rail]])
        rail_indices[rail] += 1
        rail += direction
        if rail == 0 or rail == rails - 1:
            direction = -direction
    
    return ''.join(result)


def columnar_encrypt(plaintext: str, key: str) -> str:
    """
    Columnar transposition cipher.
    
    >>> columnar_encrypt("HELLO WORLD", "KEY")
    'EWD LOLLHOR'
    """
    plaintext = plaintext.replace(" ", "")
    key = key.upper()
    cols = len(key)
    rows = (len(plaintext) + cols - 1) // cols
    
    # Pad plaintext
    padded = plaintext + 'X' * (rows * cols - len(plaintext))
    
    # Create grid
    grid = [padded[i:i + cols] for i in range(0, len(padded), cols)]
    
    # Get column order from key
    order = sorted(range(cols), key=lambda x: key[x])
    
    # Read columns in order
    result = []
    for col in order:
        for row in grid:
            result.append(row[col])
    
    return ''.join(result)


def columnar_decrypt(ciphertext: str, key: str) -> str:
    """
    Columnar transposition decryption.
    
    >>> columnar_decrypt("EWD LOLLHOR", "KEY").rstrip('X')
    'HELLOWORLD'
    """
    ciphertext = ciphertext.replace(" ", "")
    key = key.upper()
    cols = len(key)
    rows = len(ciphertext) // cols
    
    # Get column order
    order = sorted(range(cols), key=lambda x: key[x])
    
    # Split into columns
    grid = [['' for _ in range(cols)] for _ in range(rows)]
    index = 0
    for col in order:
        for row in range(rows):
            grid[row][col] = ciphertext[index]
            index += 1
    
    # Read row by row
    return ''.join(''.join(row) for row in grid)


def simple_substitution_encrypt(
    plaintext: str, 
    key: str
) -> str:
    """
    Simple substitution cipher with custom alphabet.
    
    >>> key = "QWERTYUIOPASDFGHJKLZXCVBNM"
    >>> simple_substitution_encrypt("HELLO", key)
    'ITSSG'
    """
    alphabet = string.ascii_uppercase
    key = key.upper()
    
    result = []
    for char in plaintext:
        if char.upper() in alphabet:
            index = alphabet.index(char.upper())
            new_char = key[index]
            result.append(new_char if char.isupper() else new_char.lower())
        else:
            result.append(char)
    return ''.join(result)


def frequency_analysis(text: str) -> Dict[str, float]:
    """
    Analyze letter frequency for cryptanalysis.
    
    >>> freq = frequency_analysis("AAABBC")
    >>> round(freq['A'], 2)
    0.5
    """
    text = text.upper()
    letters = [c for c in text if c.isalpha()]
    total = len(letters)
    
    if total == 0:
        return {}
    
    freq = {}
    for letter in string.ascii_uppercase:
        count = letters.count(letter)
        freq[letter] = count / total
    
    return freq


# Demo
if __name__ == "__main__":
    print("Classical Ciphers Demo")
    print("=" * 50)
    
    message = "HELLO WORLD"
    
    # Caesar
    print("\n1. Caesar Cipher (shift=3):")
    encrypted = caesar_encrypt(message, 3)
    print(f"   Original:  {message}")
    print(f"   Encrypted: {encrypted}")
    print(f"   Decrypted: {caesar_decrypt(encrypted, 3)}")
    
    # Vigenère
    print("\n2. Vigenère Cipher (key='KEY'):")
    encrypted = vigenere_encrypt(message, "KEY")
    print(f"   Original:  {message}")
    print(f"   Encrypted: {encrypted}")
    print(f"   Decrypted: {vigenere_decrypt(encrypted, 'KEY')}")
    
    # Rail Fence
    print("\n3. Rail Fence Cipher (3 rails):")
    text = "HELLOWORLD"
    encrypted = rail_fence_encrypt(text, 3)
    print(f"   Original:  {text}")
    print(f"   Encrypted: {encrypted}")
    print(f"   Decrypted: {rail_fence_decrypt(encrypted, 3)}")
    
    # Columnar
    print("\n4. Columnar Transposition (key='KEY'):")
    encrypted = columnar_encrypt(message, "KEY")
    print(f"   Original:  {message}")
    print(f"   Encrypted: {encrypted}")
    decrypted = columnar_decrypt(encrypted, "KEY")
    print(f"   Decrypted: {decrypted}")
    
    # Frequency Analysis
    print("\n5. Frequency Analysis:")
    sample = "THE QUICK BROWN FOX JUMPS OVER THE LAZY DOG"
    freq = frequency_analysis(sample)
    top_5 = sorted(freq.items(), key=lambda x: -x[1])[:5]
    print(f"   Text: {sample}")
    print(f"   Top 5: {[(l, f'{f:.1%}') for l, f in top_5]}")
```

## 8. Applications

### 8.1 Historical Uses

- **Military communication**: Caesar used by Roman army
- **Diplomatic messages**: Vigenère was "unbreakable"
- **Secret societies**: Substitution ciphers for privacy
- **Literature**: Puzzles and hidden messages

### 8.2 Modern Educational Uses

- **Cryptography courses**: Foundation concepts
- **CTF competitions**: Puzzle solving
- **Escape rooms**: Interactive puzzles
- **Teaching modular arithmetic**

## 9. Real-World Software Engineering Applications

### 9.1 Production Example: Puzzle Game Engine

```python
from typing import Dict, List, Optional, Callable
from dataclasses import dataclass
from enum import Enum
import random
import json


class CipherType(Enum):
    CAESAR = "caesar"
    VIGENERE = "vigenere"
    RAIL_FENCE = "rail_fence"
    SUBSTITUTION = "substitution"
    ATBASH = "atbash"


@dataclass
class CipherChallenge:
    """A cryptographic puzzle challenge."""
    id: str
    cipher_type: CipherType
    ciphertext: str
    hint: str
    difficulty: int  # 1-5
    key_info: Dict  # Cipher-specific key data
    plaintext: str  # For verification


class CipherPuzzleEngine:
    """
    Engine for creating and solving cipher-based puzzles.
    
    Used in escape rooms, educational games, CTF challenges.
    """
    
    def __init__(self):
        self.challenges: Dict[str, CipherChallenge] = {}
        self.solvers: Dict[CipherType, Callable] = {
            CipherType.CAESAR: self._solve_caesar,
            CipherType.VIGENERE: self._solve_vigenere,
            CipherType.RAIL_FENCE: self._solve_rail_fence,
            CipherType.SUBSTITUTION: self._solve_substitution,
            CipherType.ATBASH: self._solve_atbash,
        }
    
    def create_challenge(
        self,
        plaintext: str,
        cipher_type: CipherType,
        difficulty: int = 3,
        **kwargs
    ) -> CipherChallenge:
        """
        Create a new cipher challenge.
        
        Args:
            plaintext: Original message
            cipher_type: Type of cipher to use
            difficulty: 1-5 difficulty level
            **kwargs: Cipher-specific parameters
        
        Returns:
            CipherChallenge object
        """
        challenge_id = f"challenge_{len(self.challenges) + 1}"
        
        if cipher_type == CipherType.CAESAR:
            shift = kwargs.get('shift', random.randint(1, 25))
            ciphertext = caesar_encrypt(plaintext, shift)
            hint = f"Shift cipher with {26 - shift} backward or {shift} forward"
            key_info = {'shift': shift}
            
        elif cipher_type == CipherType.VIGENERE:
            keyword = kwargs.get('keyword', 
                                  random.choice(['KEY', 'SECRET', 'CODE']))
            ciphertext = vigenere_encrypt(plaintext, keyword)
            hint = f"Keyword length: {len(keyword)}"
            key_info = {'keyword': keyword}
            
        elif cipher_type == CipherType.RAIL_FENCE:
            rails = kwargs.get('rails', random.randint(2, 5))
            ciphertext = rail_fence_encrypt(
                plaintext.replace(" ", ""), rails
            )
            hint = f"Number of rails: {rails}"
            key_info = {'rails': rails}
            
        elif cipher_type == CipherType.SUBSTITUTION:
            alphabet = list('ABCDEFGHIJKLMNOPQRSTUVWXYZ')
            key = kwargs.get('key')
            if not key:
                shuffled = alphabet.copy()
                random.shuffle(shuffled)
                key = ''.join(shuffled)
            ciphertext = simple_substitution_encrypt(plaintext, key)
            hint = "Frequency analysis may help"
            key_info = {'key': key}
            
        elif cipher_type == CipherType.ATBASH:
            ciphertext = atbash_cipher(plaintext)
            hint = "A=Z, B=Y, C=X..."
            key_info = {}
        
        else:
            raise ValueError(f"Unknown cipher type: {cipher_type}")
        
        challenge = CipherChallenge(
            id=challenge_id,
            cipher_type=cipher_type,
            ciphertext=ciphertext,
            hint=hint,
            difficulty=difficulty,
            key_info=key_info,
            plaintext=plaintext
        )
        
        self.challenges[challenge_id] = challenge
        return challenge
    
    def verify_solution(
        self,
        challenge_id: str,
        proposed_solution: str
    ) -> bool:
        """Verify if proposed solution is correct."""
        challenge = self.challenges.get(challenge_id)
        if not challenge:
            return False
        
        # Normalize for comparison
        expected = challenge.plaintext.upper().replace(" ", "")
        proposed = proposed_solution.upper().replace(" ", "")
        
        return expected == proposed
    
    def get_hint(self, challenge_id: str, level: int = 1) -> str:
        """Get progressively more helpful hints."""
        challenge = self.challenges.get(challenge_id)
        if not challenge:
            return "Challenge not found"
        
        hints = [challenge.hint]
        
        if level >= 2:
            hints.append(f"Cipher type: {challenge.cipher_type.value}")
        
        if level >= 3 and challenge.cipher_type == CipherType.CAESAR:
            hints.append(f"First letter maps to: {challenge.ciphertext[0]}")
        
        return " | ".join(hints)
    
    def _solve_caesar(
        self,
        ciphertext: str,
        key_info: Dict
    ) -> str:
        """Solve Caesar cipher."""
        return caesar_decrypt(ciphertext, key_info['shift'])
    
    def _solve_vigenere(
        self,
        ciphertext: str,
        key_info: Dict
    ) -> str:
        """Solve Vigenère cipher."""
        return vigenere_decrypt(ciphertext, key_info['keyword'])
    
    def _solve_rail_fence(
        self,
        ciphertext: str,
        key_info: Dict
    ) -> str:
        """Solve Rail Fence cipher."""
        return rail_fence_decrypt(ciphertext, key_info['rails'])
    
    def _solve_substitution(
        self,
        ciphertext: str,
        key_info: Dict
    ) -> str:
        """Solve substitution cipher."""
        key = key_info['key']
        # Build reverse mapping
        alphabet = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'
        reverse_key = ''.join(
            alphabet[key.index(c)] for c in alphabet
        )
        return simple_substitution_encrypt(ciphertext, reverse_key)
    
    def _solve_atbash(
        self,
        ciphertext: str,
        key_info: Dict
    ) -> str:
        """Solve Atbash cipher (self-inverse)."""
        return atbash_cipher(ciphertext)
    
    def auto_solve(self, challenge_id: str) -> Optional[str]:
        """Automatically solve a challenge (for testing/validation)."""
        challenge = self.challenges.get(challenge_id)
        if not challenge:
            return None
        
        solver = self.solvers.get(challenge.cipher_type)
        if not solver:
            return None
        
        return solver(challenge.ciphertext, challenge.key_info)


class CryptanalysisHelper:
    """
    Tools for breaking classical ciphers.
    
    Used for CTF challenges and educational purposes.
    """
    
    ENGLISH_FREQ = {
        'E': 0.127, 'T': 0.091, 'A': 0.082, 'O': 0.075,
        'I': 0.070, 'N': 0.067, 'S': 0.063, 'H': 0.061,
        'R': 0.060, 'D': 0.043, 'L': 0.040, 'C': 0.028,
        'U': 0.028, 'M': 0.024, 'W': 0.024, 'F': 0.022,
        'G': 0.020, 'Y': 0.020, 'P': 0.019, 'B': 0.015,
        'V': 0.010, 'K': 0.008, 'J': 0.002, 'X': 0.002,
        'Q': 0.001, 'Z': 0.001
    }
    
    @classmethod
    def chi_squared_score(cls, text: str) -> float:
        """
        Calculate chi-squared score against English.
        
        Lower is better (more like English).
        """
        freq = frequency_analysis(text)
        total = sum(1 for c in text if c.isalpha())
        
        if total == 0:
            return float('inf')
        
        score = 0
        for letter, expected_freq in cls.ENGLISH_FREQ.items():
            observed = freq.get(letter, 0) * total
            expected = expected_freq * total
            if expected > 0:
                score += ((observed - expected) ** 2) / expected
        
        return score
    
    @classmethod
    def break_caesar(cls, ciphertext: str) -> List[tuple]:
        """
        Break Caesar cipher using frequency analysis.
        
        Returns list of (shift, decrypted, score) sorted by score.
        """
        results = []
        
        for shift in range(26):
            decrypted = caesar_decrypt(ciphertext, shift)
            score = cls.chi_squared_score(decrypted)
            results.append((shift, decrypted, score))
        
        return sorted(results, key=lambda x: x[2])
    
    @classmethod
    def estimate_vigenere_key_length(
        cls,
        ciphertext: str,
        max_length: int = 20
    ) -> List[tuple]:
        """
        Estimate Vigenère key length using Kasiski examination.
        
        Returns list of (length, score) sorted by likelihood.
        """
        text = ''.join(c for c in ciphertext.upper() if c.isalpha())
        
        # Find repeated trigrams
        trigram_positions: Dict[str, List[int]] = {}
        for i in range(len(text) - 2):
            trigram = text[i:i+3]
            if trigram not in trigram_positions:
                trigram_positions[trigram] = []
            trigram_positions[trigram].append(i)
        
        # Calculate distances
        distances = []
        for positions in trigram_positions.values():
            if len(positions) > 1:
                for i in range(len(positions) - 1):
                    distances.append(positions[i+1] - positions[i])
        
        # Find GCD of distances
        def gcd(a: int, b: int) -> int:
            while b:
                a, b = b, a % b
            return a
        
        # Score each key length
        scores = {}
        for length in range(1, max_length + 1):
            divisible = sum(1 for d in distances if d % length == 0)
            scores[length] = divisible
        
        return sorted(scores.items(), key=lambda x: -x[1])


# Demo
if __name__ == "__main__":
    print("Cipher Puzzle Engine Demo")
    print("=" * 50)
    
    engine = CipherPuzzleEngine()
    
    # Create challenges
    print("\n1. Creating Challenges:")
    
    caesar = engine.create_challenge(
        "THE SECRET IS HIDDEN",
        CipherType.CAESAR,
        shift=7
    )
    print(f"   Caesar: {caesar.ciphertext}")
    
    vigenere = engine.create_challenge(
        "ATTACK AT DAWN",
        CipherType.VIGENERE,
        keyword="LEMON"
    )
    print(f"   Vigenère: {vigenere.ciphertext}")
    
    # Verify solutions
    print("\n2. Verifying Solutions:")
    print(f"   Caesar correct: {engine.verify_solution(caesar.id, 'THE SECRET IS HIDDEN')}")
    print(f"   Caesar wrong: {engine.verify_solution(caesar.id, 'WRONG ANSWER')}")
    
    # Auto-solve
    print("\n3. Auto-solve:")
    print(f"   Caesar solved: {engine.auto_solve(caesar.id)}")
    print(f"   Vigenère solved: {engine.auto_solve(vigenere.id)}")
    
    # Cryptanalysis
    print("\n4. Breaking Caesar Cipher:")
    encrypted = caesar_encrypt("THIS IS A SECRET MESSAGE", 17)
    print(f"   Ciphertext: {encrypted}")
    
    results = CryptanalysisHelper.break_caesar(encrypted)
    print(f"   Best guess (shift={results[0][0]}): {results[0][1]}")
    print(f"   Chi-squared score: {results[0][2]:.2f}")
```

## 10. Cryptanalysis Comparison

| Cipher | Attack Method | Difficulty | Key Weakness |
|--------|---------------|------------|--------------|
| Caesar | Brute force | Easy | Only 26 keys |
| Affine | Frequency | Easy | 312 keys max |
| Vigenère | Kasiski/Frequency | Medium | Repeating key |
| Rail Fence | Pattern | Easy | Visual pattern |
| Substitution | Frequency | Medium | Letter stats |

## 11. Best Practices

1. **Never use for security**: Classical ciphers are educational only
2. **Use for obfuscation**: Not protection, just hiding
3. **Document weakness**: Make security limitations clear
4. **Layer ciphers carefully**: Multiple weak ≠ strong
5. **Preserve formatting**: Handle spaces/punctuation consistently

## 12. References

- [Wikipedia: Classical Cipher](https://en.wikipedia.org/wiki/Classical_cipher)
- Singh, S. "The Code Book"
- [Cipher Tools](https://rumkin.com/tools/cipher/)
