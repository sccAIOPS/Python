# Bit Manipulation

## Overview
- **Category**: Low-level Operations / Optimization
- **Complexity**: Time: O(1) for most operations | Space: O(1)
- **Type**: Binary arithmetic, bit operations
- **Source Files**: [bit_manipulation/](../../../bit_manipulation/)

## 1. Mathematical Foundation

### 1.1 Binary Number System

A number $n$ in binary:
$$
n = \sum_{i=0}^{k-1} b_i \cdot 2^i
$$

Where $b_i \in \{0, 1\}$ is the $i$-th bit.

### 1.2 Bitwise Operators

| Operator | Symbol | Description | Example |
|----------|--------|-------------|---------|
| AND | `&` | 1 if both bits are 1 | `5 & 3 = 1` |
| OR | `\|` | 1 if at least one bit is 1 | `5 \| 3 = 7` |
| XOR | `^` | 1 if bits differ | `5 ^ 3 = 6` |
| NOT | `~` | Flip all bits | `~5 = -6` |
| Left Shift | `<<` | Multiply by 2^n | `5 << 1 = 10` |
| Right Shift | `>>` | Divide by 2^n | `5 >> 1 = 2` |

### 1.3 Truth Tables

```
AND (&)    OR (|)     XOR (^)
A B | R    A B | R    A B | R
0 0 | 0    0 0 | 0    0 0 | 0
0 1 | 0    0 1 | 1    0 1 | 1
1 0 | 0    1 0 | 1    1 0 | 1
1 1 | 1    1 1 | 1    1 1 | 0
```

### 1.4 Two's Complement

For negative numbers in $k$ bits:
$$
-n = 2^k - n = \sim n + 1
$$

Example: `-5` in 8 bits = `11111011`

### 1.5 Useful Identities

| Identity | Formula |
|----------|---------|
| Clear lowest set bit | `n & (n - 1)` |
| Get lowest set bit | `n & (-n)` |
| Check power of 2 | `n & (n - 1) == 0` |
| Set bit at position i | `n \| (1 << i)` |
| Clear bit at position i | `n & ~(1 << i)` |
| Toggle bit at position i | `n ^ (1 << i)` |
| Check bit at position i | `(n >> i) & 1` |

### 1.6 Brian Kernighan's Algorithm

Count set bits in O(number of set bits):
```
count = 0
while n:
    n = n & (n - 1)  # Clear lowest set bit
    count += 1
```

## 2. Algorithm Categories

### 2.1 Counting Operations
- Count set bits (popcount)
- Count trailing zeros
- Count leading zeros

### 2.2 Bit Checking
- Check if power of 2
- Check if power of 4
- Check parity

### 2.3 Bit Setting/Clearing
- Set/clear/toggle specific bits
- Clear lowest/highest set bit
- Isolate bits

### 2.4 Arithmetic with Bits
- Add without + operator
- Multiply without * operator
- Divide without / operator

## 3. Pseudocode

### 3.1 Count Set Bits

```
ALGORITHM CountSetBits(n)
    INPUT: Integer n
    OUTPUT: Number of 1-bits
    
    count ← 0
    while n > 0 do
        n ← n AND (n - 1)  // Clear lowest set bit
        count ← count + 1
    return count
```

### 3.2 Check Power of Two

```
ALGORITHM IsPowerOfTwo(n)
    INPUT: Integer n
    OUTPUT: true if n is power of 2
    
    return n > 0 AND (n AND (n - 1)) = 0
```

### 3.3 Addition Using Bits

```
ALGORITHM BitwiseAdd(a, b)
    INPUT: Two integers a, b
    OUTPUT: a + b
    
    while b ≠ 0 do
        carry ← a AND b
        a ← a XOR b
        b ← carry << 1
    return a
```

### 3.4 Swap Without Temp

```
ALGORITHM BitwiseSwap(a, b)
    a ← a XOR b
    b ← a XOR b  // Now b = original a
    a ← a XOR b  // Now a = original b
```

### 3.5 Find Missing Number

```
ALGORITHM FindMissing(arr, n)
    INPUT: Array of n-1 distinct numbers from 1 to n
    OUTPUT: Missing number
    
    xor_all ← 0
    xor_arr ← 0
    
    for i ← 1 to n do
        xor_all ← xor_all XOR i
    
    for each x in arr do
        xor_arr ← xor_arr XOR x
    
    return xor_all XOR xor_arr
```

## 4. Step-by-Step Example

### Example: Count Set Bits (Brian Kernighan)

```
n = 13 = 1101 in binary

Iteration 1:
  n = 1101 (13)
  n - 1 = 1100 (12)
  n & (n-1) = 1100 (12)
  count = 1

Iteration 2:
  n = 1100 (12)
  n - 1 = 1011 (11)
  n & (n-1) = 1000 (8)
  count = 2

Iteration 3:
  n = 1000 (8)
  n - 1 = 0111 (7)
  n & (n-1) = 0000 (0)
  count = 3

n = 0, loop ends

Result: 3 set bits
```

### Example: Bitwise Addition

```
a = 5 = 0101
b = 3 = 0011

Iteration 1:
  carry = 0101 & 0011 = 0001
  a = 0101 ^ 0011 = 0110 (6)
  b = 0001 << 1 = 0010 (2)

Iteration 2:
  carry = 0110 & 0010 = 0010
  a = 0110 ^ 0010 = 0100 (4)
  b = 0010 << 1 = 0100 (4)

Iteration 3:
  carry = 0100 & 0100 = 0100
  a = 0100 ^ 0100 = 0000 (0)
  b = 0100 << 1 = 1000 (8)

Iteration 4:
  carry = 0000 & 1000 = 0000
  a = 0000 ^ 1000 = 1000 (8)
  b = 0000 << 1 = 0000

b = 0, loop ends

Result: a = 8 = 5 + 3 ✓
```

## 5. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| AND, OR, XOR, NOT | O(1) | O(1) |
| Shift | O(1) | O(1) |
| Count bits (naive) | O(k) | O(1) |
| Count bits (Kernighan) | O(s) | O(1) |
| Find missing XOR | O(n) | O(1) |

Where k = number of bits, s = number of set bits

## 6. Visual Representation

### 6.1 Bit Operations

```
AND (&):          OR (|):           XOR (^):
  0101 (5)          0101 (5)          0101 (5)
& 0011 (3)        | 0011 (3)        ^ 0011 (3)
─────────         ─────────         ─────────
  0001 (1)          0111 (7)          0110 (6)
```

### 6.2 Clearing Lowest Set Bit

```
n = 12 = 1100
n - 1 = 11 = 1011

n & (n-1):
  1100
& 1011
──────
  1000 = 8

Lowest set bit (position 2) is cleared!
```

### 6.3 Isolating Lowest Set Bit

```
n = 12 = 1100
-n = -12 = ...11110100 (two's complement)

n & (-n):
  ...00001100
& ...11110100
─────────────
  ...00000100 = 4

Lowest set bit isolated!
```

## 7. Implementation

```python
from typing import List, Tuple, Optional


def count_set_bits(n: int) -> int:
    """
    Count number of 1-bits using Brian Kernighan's algorithm.
    
    Time: O(number of set bits)
    
    >>> count_set_bits(13)  # 1101
    3
    >>> count_set_bits(0)
    0
    >>> count_set_bits(255)  # 11111111
    8
    """
    count = 0
    while n:
        n &= (n - 1)
        count += 1
    return count


def count_set_bits_lookup(n: int) -> int:
    """
    Count set bits using lookup table.
    
    Time: O(1) for 32-bit integers
    
    >>> count_set_bits_lookup(13)
    3
    """
    # Precomputed table for 8-bit values
    LOOKUP = [bin(i).count('1') for i in range(256)]
    
    count = 0
    while n:
        count += LOOKUP[n & 0xFF]
        n >>= 8
    return count


def is_power_of_two(n: int) -> bool:
    """
    Check if n is a power of 2.
    
    >>> is_power_of_two(16)
    True
    >>> is_power_of_two(18)
    False
    >>> is_power_of_two(1)
    True
    """
    return n > 0 and (n & (n - 1)) == 0


def is_power_of_four(n: int) -> bool:
    """
    Check if n is a power of 4.
    
    >>> is_power_of_four(16)
    True
    >>> is_power_of_four(8)
    False
    """
    # Power of 4 has single bit at even position
    # 0x55555555 = 01010101... in binary (even positions)
    return n > 0 and (n & (n - 1)) == 0 and (n & 0x55555555) != 0


def get_bit(n: int, i: int) -> int:
    """
    Get the i-th bit of n.
    
    >>> get_bit(13, 2)  # 1101, bit at position 2
    1
    >>> get_bit(13, 1)
    0
    """
    return (n >> i) & 1


def set_bit(n: int, i: int) -> int:
    """
    Set the i-th bit of n to 1.
    
    >>> set_bit(13, 1)  # 1101 -> 1111 = 15
    15
    """
    return n | (1 << i)


def clear_bit(n: int, i: int) -> int:
    """
    Clear the i-th bit of n to 0.
    
    >>> clear_bit(13, 2)  # 1101 -> 1001 = 9
    9
    """
    return n & ~(1 << i)


def toggle_bit(n: int, i: int) -> int:
    """
    Toggle the i-th bit of n.
    
    >>> toggle_bit(13, 1)  # 1101 -> 1111 = 15
    15
    >>> toggle_bit(15, 1)  # 1111 -> 1101 = 13
    13
    """
    return n ^ (1 << i)


def lowest_set_bit(n: int) -> int:
    """
    Get the lowest set bit.
    
    >>> lowest_set_bit(12)  # 1100 -> 0100 = 4
    4
    >>> lowest_set_bit(0)
    0
    """
    return n & (-n)


def clear_lowest_set_bit(n: int) -> int:
    """
    Clear the lowest set bit.
    
    >>> clear_lowest_set_bit(12)  # 1100 -> 1000 = 8
    8
    """
    return n & (n - 1)


def highest_set_bit(n: int) -> int:
    """
    Get position of highest set bit.
    
    >>> highest_set_bit(12)  # 1100
    3
    >>> highest_set_bit(1)
    0
    """
    if n == 0:
        return -1
    
    position = 0
    while n > 1:
        n >>= 1
        position += 1
    return position


def count_trailing_zeros(n: int) -> int:
    """
    Count trailing zeros.
    
    >>> count_trailing_zeros(12)  # 1100
    2
    >>> count_trailing_zeros(1)
    0
    """
    if n == 0:
        return 32  # Assume 32-bit
    
    count = 0
    while (n & 1) == 0:
        n >>= 1
        count += 1
    return count


def add_without_operator(a: int, b: int) -> int:
    """
    Add two numbers without using + operator.
    
    >>> add_without_operator(5, 3)
    8
    >>> add_without_operator(-1, 1)
    0
    """
    # Handle negative numbers in Python
    MASK = 0xFFFFFFFF
    MAX_INT = 0x7FFFFFFF
    
    while b != 0:
        carry = (a & b) & MASK
        a = (a ^ b) & MASK
        b = (carry << 1) & MASK
    
    return a if a <= MAX_INT else ~(a ^ MASK)


def multiply_without_operator(a: int, b: int) -> int:
    """
    Multiply without using * operator.
    
    Uses shift and add.
    
    >>> multiply_without_operator(5, 3)
    15
    >>> multiply_without_operator(7, 0)
    0
    """
    negative = (a < 0) ^ (b < 0)
    a, b = abs(a), abs(b)
    
    result = 0
    while b:
        if b & 1:
            result += a
        a <<= 1
        b >>= 1
    
    return -result if negative else result


def find_missing_number(arr: List[int], n: int) -> int:
    """
    Find missing number in array [1, n] with one missing.
    
    Uses XOR property: a ^ a = 0
    
    >>> find_missing_number([1, 2, 4, 5], 5)
    3
    """
    xor_all = 0
    xor_arr = 0
    
    for i in range(1, n + 1):
        xor_all ^= i
    
    for x in arr:
        xor_arr ^= x
    
    return xor_all ^ xor_arr


def find_unique_number(arr: List[int]) -> int:
    """
    Find the number that appears once (others appear twice).
    
    >>> find_unique_number([2, 3, 2, 4, 4])
    3
    """
    result = 0
    for x in arr:
        result ^= x
    return result


def find_two_unique_numbers(arr: List[int]) -> Tuple[int, int]:
    """
    Find two numbers that appear once (others appear twice).
    
    >>> sorted(find_two_unique_numbers([2, 3, 2, 4, 4, 5]))
    [3, 5]
    """
    # XOR all numbers
    xor_all = 0
    for x in arr:
        xor_all ^= x
    
    # Find rightmost set bit (differs between the two unique numbers)
    rightmost = xor_all & (-xor_all)
    
    # Partition into two groups
    num1 = 0
    num2 = 0
    
    for x in arr:
        if x & rightmost:
            num1 ^= x
        else:
            num2 ^= x
    
    return (num1, num2)


def reverse_bits(n: int, bits: int = 32) -> int:
    """
    Reverse bits of a number.
    
    >>> bin(reverse_bits(0b1010, 4))
    '0b101'
    """
    result = 0
    for _ in range(bits):
        result = (result << 1) | (n & 1)
        n >>= 1
    return result


def swap_bits(n: int, i: int, j: int) -> int:
    """
    Swap bits at positions i and j.
    
    >>> bin(swap_bits(0b1010, 1, 3))
    '0b10'
    """
    if get_bit(n, i) != get_bit(n, j):
        n ^= (1 << i) | (1 << j)
    return n


class BitVector:
    """
    Efficient bit vector/set implementation.
    """
    
    def __init__(self, size: int):
        """
        Initialize bit vector of given size.
        
        >>> bv = BitVector(100)
        >>> bv.set(50)
        >>> bv.test(50)
        True
        """
        self.size = size
        self.words = [0] * ((size + 63) // 64)
    
    def set(self, index: int) -> None:
        """Set bit at index."""
        if 0 <= index < self.size:
            self.words[index // 64] |= (1 << (index % 64))
    
    def clear(self, index: int) -> None:
        """Clear bit at index."""
        if 0 <= index < self.size:
            self.words[index // 64] &= ~(1 << (index % 64))
    
    def test(self, index: int) -> bool:
        """Test if bit at index is set."""
        if 0 <= index < self.size:
            return bool(self.words[index // 64] & (1 << (index % 64)))
        return False
    
    def toggle(self, index: int) -> None:
        """Toggle bit at index."""
        if 0 <= index < self.size:
            self.words[index // 64] ^= (1 << (index % 64))
    
    def count(self) -> int:
        """Count set bits."""
        return sum(bin(w).count('1') for w in self.words)


# Demo
if __name__ == "__main__":
    print("Bit Manipulation Demo")
    print("=" * 50)
    
    # Count set bits
    print("\n1. Count Set Bits:")
    n = 13
    print(f"   {n} = {bin(n)} has {count_set_bits(n)} set bits")
    
    # Power of 2
    print("\n2. Power of 2 Check:")
    for n in [1, 2, 3, 4, 16, 18]:
        print(f"   {n}: power of 2? {is_power_of_two(n)}")
    
    # Bit operations
    print("\n3. Bit Operations:")
    n = 13
    print(f"   Original: {n} = {bin(n)}")
    print(f"   Get bit 2: {get_bit(n, 2)}")
    print(f"   Set bit 1: {set_bit(n, 1)} = {bin(set_bit(n, 1))}")
    print(f"   Clear bit 2: {clear_bit(n, 2)} = {bin(clear_bit(n, 2))}")
    
    # Addition without +
    print("\n4. Addition Without + Operator:")
    a, b = 5, 3
    result = add_without_operator(a, b)
    print(f"   {a} + {b} = {result}")
    
    # Find missing
    print("\n5. Find Missing Number:")
    arr = [1, 2, 4, 5]
    n = 5
    print(f"   Array: {arr}, n={n}")
    print(f"   Missing: {find_missing_number(arr, n)}")
    
    # Find unique
    print("\n6. Find Unique Number:")
    arr = [2, 3, 2, 4, 4]
    print(f"   Array: {arr}")
    print(f"   Unique: {find_unique_number(arr)}")
    
    # Bit vector
    print("\n7. Bit Vector:")
    bv = BitVector(100)
    bv.set(10)
    bv.set(50)
    bv.set(99)
    print(f"   Set bits: 10, 50, 99")
    print(f"   Count: {bv.count()}")
    print(f"   Test 50: {bv.test(50)}")
    print(f"   Test 51: {bv.test(51)}")
```

## 8. Applications

### 8.1 Common Use Cases

- **Flags and permissions**: User roles, file permissions
- **Set operations**: Bloom filters, bit sets
- **Optimization**: Replace multiplication/division with shifts
- **Cryptography**: XOR encryption, hash functions
- **Error detection**: Parity bits, checksums
- **Compression**: Huffman encoding, run-length

### 8.2 Related Problems

| Problem | Technique |
|---------|-----------|
| Single Number | XOR all elements |
| Missing Number | XOR 1..n with array |
| Power of 2 | n & (n-1) == 0 |
| Hamming Distance | XOR then count bits |
| Reverse Bits | Shift and OR |

## 9. Real-World Software Engineering Applications

### 9.1 Production Example: Permission System

```python
from typing import Dict, List, Set
from enum import IntFlag, auto
from dataclasses import dataclass
import json


class Permission(IntFlag):
    """File/resource permissions using bit flags."""
    NONE = 0
    READ = auto()      # 1
    WRITE = auto()     # 2
    EXECUTE = auto()   # 4
    DELETE = auto()    # 8
    ADMIN = auto()     # 16
    
    # Common combinations
    READ_WRITE = READ | WRITE
    READ_EXECUTE = READ | EXECUTE
    FULL = READ | WRITE | EXECUTE | DELETE
    ALL = READ | WRITE | EXECUTE | DELETE | ADMIN


class Role(IntFlag):
    """User roles."""
    GUEST = auto()     # 1
    USER = auto()      # 2
    EDITOR = auto()    # 4
    MODERATOR = auto() # 8
    ADMIN = auto()     # 16
    SUPERUSER = auto() # 32


@dataclass
class User:
    """User with role-based permissions."""
    id: int
    username: str
    roles: int = Role.GUEST
    
    def has_role(self, role: Role) -> bool:
        return bool(self.roles & role)
    
    def add_role(self, role: Role):
        self.roles |= role
    
    def remove_role(self, role: Role):
        self.roles &= ~role
    
    def get_roles(self) -> List[str]:
        return [r.name for r in Role if self.roles & r]


class PermissionManager:
    """
    Manage resource permissions using bit manipulation.
    
    Efficient O(1) permission checks.
    """
    
    # Role -> Permission mapping
    ROLE_PERMISSIONS = {
        Role.GUEST: Permission.READ,
        Role.USER: Permission.READ | Permission.WRITE,
        Role.EDITOR: Permission.READ | Permission.WRITE | Permission.DELETE,
        Role.MODERATOR: Permission.READ | Permission.WRITE | Permission.DELETE,
        Role.ADMIN: Permission.FULL,
        Role.SUPERUSER: Permission.ALL
    }
    
    def __init__(self):
        self.resource_permissions: Dict[str, Dict[int, int]] = {}
    
    def set_permission(
        self,
        resource: str,
        user_id: int,
        permissions: Permission
    ):
        """Set permissions for user on resource."""
        if resource not in self.resource_permissions:
            self.resource_permissions[resource] = {}
        self.resource_permissions[resource][user_id] = int(permissions)
    
    def get_permission(
        self,
        resource: str,
        user_id: int
    ) -> Permission:
        """Get permissions for user on resource."""
        if resource not in self.resource_permissions:
            return Permission.NONE
        return Permission(
            self.resource_permissions[resource].get(user_id, 0)
        )
    
    def check_permission(
        self,
        resource: str,
        user: User,
        required: Permission
    ) -> bool:
        """
        Check if user has required permission on resource.
        
        Combines explicit permissions with role-based permissions.
        """
        # Get explicit permission
        explicit = self.get_permission(resource, user.id)
        
        # Get role-based permission
        role_perm = Permission.NONE
        for role in Role:
            if user.has_role(role):
                role_perm |= self.ROLE_PERMISSIONS.get(role, Permission.NONE)
        
        # Combine permissions
        effective = explicit | role_perm
        
        # Check if required permission is satisfied
        return (effective & required) == required
    
    def grant_permission(
        self,
        resource: str,
        user_id: int,
        permission: Permission
    ):
        """Add permission without removing existing."""
        current = self.get_permission(resource, user_id)
        self.set_permission(resource, user_id, current | permission)
    
    def revoke_permission(
        self,
        resource: str,
        user_id: int,
        permission: Permission
    ):
        """Remove specific permission."""
        current = self.get_permission(resource, user_id)
        self.set_permission(resource, user_id, current & ~permission)


class BloomFilter:
    """
    Space-efficient probabilistic set.
    
    Uses bit array with multiple hash functions.
    """
    
    def __init__(self, size: int = 1000, num_hashes: int = 3):
        """
        Initialize Bloom filter.
        
        Args:
            size: Number of bits
            num_hashes: Number of hash functions
        """
        self.size = size
        self.num_hashes = num_hashes
        self.bits = [0] * ((size + 63) // 64)
    
    def _hash(self, item: str, seed: int) -> int:
        """Generate hash for item with seed."""
        h = hash(item + str(seed))
        return abs(h) % self.size
    
    def _set_bit(self, index: int):
        """Set bit at index."""
        word_idx = index // 64
        bit_idx = index % 64
        self.bits[word_idx] |= (1 << bit_idx)
    
    def _test_bit(self, index: int) -> bool:
        """Test bit at index."""
        word_idx = index // 64
        bit_idx = index % 64
        return bool(self.bits[word_idx] & (1 << bit_idx))
    
    def add(self, item: str):
        """Add item to filter."""
        for i in range(self.num_hashes):
            index = self._hash(item, i)
            self._set_bit(index)
    
    def contains(self, item: str) -> bool:
        """
        Check if item might be in filter.
        
        False positives possible, no false negatives.
        """
        for i in range(self.num_hashes):
            index = self._hash(item, i)
            if not self._test_bit(index):
                return False
        return True
    
    def false_positive_rate(self, items_added: int) -> float:
        """Estimate false positive rate."""
        import math
        k = self.num_hashes
        m = self.size
        n = items_added
        
        return (1 - math.exp(-k * n / m)) ** k


class FeatureFlags:
    """
    Feature flag management using bit fields.
    
    Efficient feature toggling for A/B testing.
    """
    
    def __init__(self):
        self.flags: int = 0
        self.flag_names: Dict[int, str] = {}
        self.next_bit = 0
    
    def register_flag(self, name: str) -> int:
        """Register a new feature flag."""
        bit = 1 << self.next_bit
        self.flag_names[bit] = name
        self.next_bit += 1
        return bit
    
    def enable(self, flag: int):
        """Enable a feature."""
        self.flags |= flag
    
    def disable(self, flag: int):
        """Disable a feature."""
        self.flags &= ~flag
    
    def toggle(self, flag: int):
        """Toggle a feature."""
        self.flags ^= flag
    
    def is_enabled(self, flag: int) -> bool:
        """Check if feature is enabled."""
        return bool(self.flags & flag)
    
    def get_enabled_flags(self) -> List[str]:
        """Get list of enabled feature names."""
        return [
            name for bit, name in self.flag_names.items()
            if self.flags & bit
        ]
    
    def export_state(self) -> int:
        """Export current flag state."""
        return self.flags
    
    def import_state(self, state: int):
        """Import flag state."""
        self.flags = state


# Demo
if __name__ == "__main__":
    print("Permission System Demo")
    print("=" * 50)
    
    # User with roles
    user = User(1, "alice")
    user.add_role(Role.USER)
    user.add_role(Role.EDITOR)
    
    print(f"\n1. User Roles:")
    print(f"   User: {user.username}")
    print(f"   Roles: {user.get_roles()}")
    print(f"   Has ADMIN: {user.has_role(Role.ADMIN)}")
    
    # Permission manager
    pm = PermissionManager()
    pm.set_permission("document.txt", 1, Permission.READ_WRITE)
    
    print(f"\n2. Permission Checks:")
    print(f"   Can read: {pm.check_permission('document.txt', user, Permission.READ)}")
    print(f"   Can delete: {pm.check_permission('document.txt', user, Permission.DELETE)}")
    
    # Bloom filter
    print(f"\n3. Bloom Filter:")
    bf = BloomFilter(1000, 3)
    
    words = ["apple", "banana", "cherry"]
    for word in words:
        bf.add(word)
    
    print(f"   Contains 'apple': {bf.contains('apple')}")
    print(f"   Contains 'grape': {bf.contains('grape')}")
    print(f"   FP rate: {bf.false_positive_rate(len(words)):.4f}")
    
    # Feature flags
    print(f"\n4. Feature Flags:")
    ff = FeatureFlags()
    
    DARK_MODE = ff.register_flag("dark_mode")
    NEW_UI = ff.register_flag("new_ui")
    BETA_FEATURES = ff.register_flag("beta_features")
    
    ff.enable(DARK_MODE)
    ff.enable(NEW_UI)
    
    print(f"   Enabled flags: {ff.get_enabled_flags()}")
    print(f"   State: {bin(ff.export_state())}")
```

## 10. Comparison

| Operation | Bit Method | Alternative | Speedup |
|-----------|------------|-------------|---------|
| Multiply by 2 | `n << 1` | `n * 2` | 2-5x |
| Divide by 2 | `n >> 1` | `n // 2` | 2-5x |
| Check even | `n & 1 == 0` | `n % 2 == 0` | 2-3x |
| Power of 2 | `n & (n-1) == 0` | Loop/math | 10x+ |

## 11. Best Practices

1. **Use named constants** for bit positions
2. **Document bit layouts** for packed structures
3. **Handle negative numbers** carefully (two's complement)
4. **Use IntFlag** for type-safe bit operations
5. **Test edge cases**: 0, negative, overflow

## 12. References

- [Wikipedia: Bitwise Operations](https://en.wikipedia.org/wiki/Bitwise_operation)
- Hacker's Delight by Henry S. Warren
- [Bit Twiddling Hacks](https://graphics.stanford.edu/~seander/bithacks.html)
