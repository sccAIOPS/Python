# Subset Sum Problem

## Overview
- **Category**: Backtracking / Dynamic Programming
- **Complexity**: Time: O(2^n) backtracking, O(n×S) DP | Space: O(n) to O(n×S)
- **Type**: Combinatorial optimization / Decision problem
- **Source Files**: [backtracking/sum_of_subsets.py](../../../backtracking/sum_of_subsets.py)

## 1. Mathematical Foundation

### 1.1 Problem Definition

Given a set of $n$ positive integers $X = \{x_1, x_2, ..., x_n\}$ and a target sum $S$:

**Decision Problem**: Is there a subset of $X$ that sums to $S$?

$$
\exists T \subseteq X : \sum_{x \in T} x = S
$$

**Optimization Problem**: Find the subset(s) that sum to $S$.

### 1.2 Mathematical Properties

**Subset representation** using indicator variables:
$$
\sum_{i=1}^{n} b_i \cdot x_i = S, \quad b_i \in \{0, 1\}
$$

**Total subsets**: $2^n$ possible subsets.

### 1.3 Upper Bound Pruning

Before exploring, compute **remaining sum**:
$$
\text{remaining} = \sum_{j=i}^{n} x_j
$$

**Prune if**: $\text{current\_sum} + \text{remaining} < S$

### 1.4 Lower Bound Pruning

If array is **sorted in ascending order**:
- **Prune if**: $\text{current\_sum} + x_i > S$ (even smallest remaining is too large)

### 1.5 NP-Completeness

Subset Sum is **NP-Complete**:
- Decision version is in NP (verify in polynomial time)
- Reduces from 3-SAT
- Special case of 0/1 Knapsack

### 1.6 Pseudo-polynomial Solution

When target $S$ is bounded, DP runs in $O(nS)$ - polynomial in numeric value but exponential in input size (number of bits).

## 2. Algorithm Approaches

### 2.1 Backtracking
- Explore each element: include or exclude
- Prune using bounds

### 2.2 Dynamic Programming
- Build table of achievable sums
- Space optimized to O(S)

### 2.3 Meet in the Middle
- Split array in half
- Find all sums for each half
- Binary search for complements
- Complexity: $O(2^{n/2} \cdot n)$

## 3. Pseudocode

### 3.1 Backtracking

```
ALGORITHM SubsetSumBacktrack(arr, target)
    INPUT: Array of positive integers, target sum
    OUTPUT: All subsets summing to target
    
    Sort arr in descending order (optional, for pruning)
    result ← []
    
    FUNCTION Backtrack(index, current_sum, current_subset)
        // Success case
        if current_sum = target then
            result.append(copy of current_subset)
            return
        
        // Base case - no more elements
        if index >= n then
            return
        
        // Pruning: remaining sum too small
        if current_sum + remaining[index] < target then
            return
        
        // Pruning: adding current exceeds target
        if current_sum + arr[index] > target then
            // Skip to smaller elements (if sorted descending)
            Backtrack(index + 1, current_sum, current_subset)
            return
        
        // Include current element
        current_subset.add(arr[index])
        Backtrack(index + 1, current_sum + arr[index], current_subset)
        current_subset.remove(arr[index])
        
        // Exclude current element
        Backtrack(index + 1, current_sum, current_subset)
    
    Backtrack(0, 0, [])
    return result
```

### 3.2 Dynamic Programming (Decision)

```
ALGORITHM SubsetSumDP(arr, target)
    INPUT: Array of positive integers, target sum
    OUTPUT: True if subset with target sum exists
    
    // dp[j] = true if sum j is achievable
    dp ← array of size (target + 1), initialized to false
    dp[0] ← true  // Empty subset sums to 0
    
    for each num in arr do
        // Iterate backwards to avoid using same element twice
        for j ← target down to num do
            if dp[j - num] then
                dp[j] ← true
    
    return dp[target]
```

### 3.3 DP with Subset Reconstruction

```
ALGORITHM SubsetSumWithReconstruction(arr, target)
    INPUT: Array, target
    OUTPUT: One subset summing to target (or None)
    
    n ← length(arr)
    
    // dp[i][j] = true if sum j achievable using first i elements
    dp ← 2D array [n+1][target+1], initialized to false
    dp[0][0] ← true
    
    for i ← 1 to n do
        for j ← 0 to target do
            // Exclude arr[i-1]
            dp[i][j] ← dp[i-1][j]
            
            // Include arr[i-1]
            if j >= arr[i-1] AND dp[i-1][j - arr[i-1]] then
                dp[i][j] ← true
    
    if not dp[n][target] then
        return None
    
    // Reconstruct subset
    subset ← []
    i, j ← n, target
    
    while i > 0 AND j > 0 do
        if dp[i][j] ≠ dp[i-1][j] then
            // arr[i-1] was included
            subset.append(arr[i-1])
            j ← j - arr[i-1]
        i ← i - 1
    
    return subset
```

### 3.4 Meet in the Middle

```
ALGORITHM SubsetSumMeetMiddle(arr, target)
    INPUT: Array, target
    OUTPUT: True if subset with target sum exists
    
    n ← length(arr)
    mid ← n / 2
    
    // Generate all subset sums for first half
    left_sums ← set()
    for mask ← 0 to 2^mid - 1 do
        sum ← 0
        for i ← 0 to mid - 1 do
            if mask & (1 << i) then
                sum ← sum + arr[i]
        left_sums.add(sum)
    
    // Check second half
    for mask ← 0 to 2^(n-mid) - 1 do
        sum ← 0
        for i ← 0 to (n - mid) - 1 do
            if mask & (1 << i) then
                sum ← sum + arr[mid + i]
        
        if (target - sum) in left_sums then
            return true
    
    return false
```

## 4. Step-by-Step Example

### Example: arr = [3, 5, 6, 7], target = 14

**Backtracking Tree:**
```
                        Start (sum=0)
                       /           \
                 +3 (sum=3)        skip 3 (sum=0)
                /        \              /      \
          +5 (sum=8)   skip 5     +5 (sum=5)  skip 5
          /     \        |          /    \       |
     +6(14)✓  skip   +6(9)      +6(11)  skip   ...
               |        |          |
            +7(15)✗  +7(16)✗   +7(18)✗
```

**Solution found**: {3, 5, 6} = 14 ✓

**DP Table:**
```
Elements: [3, 5, 6, 7]
Target: 14

j:        0  1  2  3  4  5  6  7  8  9  10 11 12 13 14
Initial:  T  F  F  F  F  F  F  F  F  F  F  F  F  F  F
After 3:  T  F  F  T  F  F  F  F  F  F  F  F  F  F  F
After 5:  T  F  F  T  F  T  F  F  T  F  F  F  F  F  F
After 6:  T  F  F  T  F  T  T  F  T  T  F  T  F  F  T
After 7:  T  F  F  T  F  T  T  T  T  T  T  T  T  T  T

dp[14] = True → Solution exists!
```

**Reconstruction:**
```
dp[4][14] = True, dp[3][14] = True  → 7 not used
dp[3][14] = True, dp[2][14] = False → 6 used! j = 14-6 = 8
dp[2][8] = True, dp[1][8] = False   → 5 used! j = 8-5 = 3
dp[1][3] = True, dp[0][3] = False   → 3 used! j = 3-3 = 0

Subset: {3, 5, 6} = 14 ✓
```

## 5. Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Backtracking | O(2^n) | O(n) | Worst case, better with pruning |
| DP | O(n × S) | O(S) | Pseudo-polynomial |
| DP 2D | O(n × S) | O(n × S) | For reconstruction |
| Meet in Middle | O(2^(n/2) × n) | O(2^(n/2)) | Better for large n, small S |

## 6. Visual Representation

### 6.1 State Space Tree

```
                              []
                           sum=0
                        /         \
                    [3]            []
                  sum=3          sum=0
                 /     \         /    \
            [3,5]      [3]    [5]      []
           sum=8     sum=3   sum=5   sum=0
           /   \      |       |       |
      [3,5,6] [3,5] [3,6]   [5,6]    ...
      sum=14✓ sum=8 sum=9  sum=11
```

### 6.2 DP Visualization

```
     Target Sum →
   0  1  2  3  4  5  6  7  8  9  10 11 12 13 14
   +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
   |T |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  empty
   +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
   |T |  |  |T |  |  |  |  |  |  |  |  |  |  |  |  +3
   +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
   |T |  |  |T |  |T |  |  |T |  |  |  |  |  |  |  +5
   +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
   |T |  |  |T |  |T |T |  |T |T |  |T |  |  |T |  +6
   +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
   |T |  |  |T |  |T |T |T |T |T |T |T |T |T |T |  +7
   +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+

Legend: T = achievable, blank = not achievable
```

## 7. Implementation

```python
from typing import List, Optional, Set, Tuple, Generator
from dataclasses import dataclass


def subset_sum_exists(arr: List[int], target: int) -> bool:
    """
    Check if any subset sums to target (DP approach).
    
    >>> subset_sum_exists([3, 5, 6, 7], 14)
    True
    >>> subset_sum_exists([3, 5, 6, 7], 15)
    True
    >>> subset_sum_exists([3, 5, 6, 7], 4)
    False
    >>> subset_sum_exists([1, 2, 3], 6)
    True
    >>> subset_sum_exists([], 0)
    True
    >>> subset_sum_exists([], 1)
    False
    """
    if target == 0:
        return True
    if not arr or target < 0:
        return target == 0
    
    # DP with space optimization
    dp = [False] * (target + 1)
    dp[0] = True
    
    for num in arr:
        if num <= target:
            # Iterate backwards
            for j in range(target, num - 1, -1):
                if dp[j - num]:
                    dp[j] = True
    
    return dp[target]


def subset_sum_find_one(arr: List[int], target: int) -> Optional[List[int]]:
    """
    Find one subset summing to target.
    
    >>> result = subset_sum_find_one([3, 5, 6, 7], 14)
    >>> sum(result) if result else None
    14
    >>> subset_sum_find_one([1, 2, 3], 7) is None
    True
    """
    if target == 0:
        return []
    if not arr:
        return None
    
    n = len(arr)
    
    # 2D DP for reconstruction
    dp = [[False] * (target + 1) for _ in range(n + 1)]
    dp[0][0] = True
    
    for i in range(1, n + 1):
        for j in range(target + 1):
            dp[i][j] = dp[i - 1][j]
            if j >= arr[i - 1] and dp[i - 1][j - arr[i - 1]]:
                dp[i][j] = True
    
    if not dp[n][target]:
        return None
    
    # Reconstruct
    result = []
    i, j = n, target
    
    while i > 0 and j > 0:
        if dp[i][j] and not dp[i - 1][j]:
            result.append(arr[i - 1])
            j -= arr[i - 1]
        i -= 1
    
    return result


def subset_sum_all(arr: List[int], target: int) -> List[List[int]]:
    """
    Find all subsets summing to target (backtracking).
    
    >>> subsets = subset_sum_all([3, 5, 6, 7], 14)
    >>> len(subsets)
    2
    >>> sorted([sorted(s) for s in subsets])
    [[3, 5, 6], [7, 7]]  # Depends on implementation
    """
    result = []
    n = len(arr)
    
    def backtrack(index: int, current_sum: int, current: List[int]):
        if current_sum == target:
            result.append(current.copy())
            return
        
        if index >= n or current_sum > target:
            return
        
        # Include
        current.append(arr[index])
        backtrack(index + 1, current_sum + arr[index], current)
        current.pop()
        
        # Exclude
        backtrack(index + 1, current_sum, current)
    
    backtrack(0, 0, [])
    return result


class SubsetSum:
    """
    Comprehensive subset sum solver with multiple algorithms.
    """
    
    def __init__(self, arr: List[int]):
        """
        Initialize with array.
        
        >>> ss = SubsetSum([3, 5, 6, 7])
        >>> ss.exists(14)
        True
        """
        self.arr = sorted(arr)  # Sort for pruning
        self.n = len(arr)
        self._precompute_suffix_sums()
    
    def _precompute_suffix_sums(self):
        """Precompute suffix sums for pruning."""
        self.suffix_sum = [0] * (self.n + 1)
        for i in range(self.n - 1, -1, -1):
            self.suffix_sum[i] = self.suffix_sum[i + 1] + self.arr[i]
    
    def exists(self, target: int) -> bool:
        """Check if target sum is achievable (DP)."""
        return subset_sum_exists(self.arr, target)
    
    def find_one(self, target: int) -> Optional[List[int]]:
        """Find one subset summing to target."""
        return subset_sum_find_one(self.arr, target)
    
    def find_all(self, target: int) -> List[List[int]]:
        """
        Find all subsets summing to target with pruning.
        
        >>> ss = SubsetSum([1, 2, 3, 4, 5])
        >>> subsets = ss.find_all(5)
        >>> sorted([sorted(s) for s in subsets])
        [[1, 4], [2, 3], [5]]
        """
        result = []
        
        def backtrack(index: int, remaining: int, current: List[int]):
            if remaining == 0:
                result.append(current.copy())
                return
            
            if index >= self.n:
                return
            
            # Pruning: remaining sum too small
            if self.suffix_sum[index] < remaining:
                return
            
            # Pruning: smallest remaining element too large
            if self.arr[index] > remaining:
                return
            
            # Include current
            current.append(self.arr[index])
            backtrack(index + 1, remaining - self.arr[index], current)
            current.pop()
            
            # Exclude current - skip duplicates
            next_index = index + 1
            while next_index < self.n and self.arr[next_index] == self.arr[index]:
                next_index += 1
            backtrack(next_index, remaining, current)
        
        backtrack(0, target, [])
        return result
    
    def count_subsets(self, target: int) -> int:
        """
        Count number of subsets summing to target.
        
        >>> ss = SubsetSum([1, 2, 3, 4, 5])
        >>> ss.count_subsets(5)
        3
        """
        if target < 0:
            return 0
        
        # dp[j] = number of subsets summing to j
        dp = [0] * (target + 1)
        dp[0] = 1
        
        for num in self.arr:
            if num <= target:
                for j in range(target, num - 1, -1):
                    dp[j] += dp[j - num]
        
        return dp[target]


class SubsetSumMeetMiddle:
    """
    Meet-in-the-middle approach for subset sum.
    Better for large n when target is reasonable.
    """
    
    def __init__(self, arr: List[int]):
        self.arr = arr
        self.n = len(arr)
    
    def exists(self, target: int) -> bool:
        """
        Check existence using meet in the middle.
        
        >>> ss = SubsetSumMeetMiddle([1, 2, 3, 4, 5, 6, 7, 8])
        >>> ss.exists(15)
        True
        >>> ss.exists(100)
        False
        """
        mid = self.n // 2
        
        # Generate all sums for left half
        left_sums: Set[int] = set()
        for mask in range(1 << mid):
            total = sum(self.arr[i] for i in range(mid) if mask & (1 << i))
            left_sums.add(total)
        
        # Check right half
        for mask in range(1 << (self.n - mid)):
            total = sum(
                self.arr[mid + i] 
                for i in range(self.n - mid) 
                if mask & (1 << i)
            )
            if target - total in left_sums:
                return True
        
        return False
    
    def find_one(self, target: int) -> Optional[List[int]]:
        """Find one subset using meet in the middle."""
        mid = self.n // 2
        
        # Map sums to their elements
        left_map: dict = {}
        for mask in range(1 << mid):
            elements = [self.arr[i] for i in range(mid) if mask & (1 << i)]
            total = sum(elements)
            if total not in left_map:
                left_map[total] = elements
        
        # Check right half
        for mask in range(1 << (self.n - mid)):
            elements = [
                self.arr[mid + i] 
                for i in range(self.n - mid) 
                if mask & (1 << i)
            ]
            total = sum(elements)
            complement = target - total
            
            if complement in left_map:
                return left_map[complement] + elements
        
        return None


class PartitionEqualSum:
    """
    Special case: Can array be partitioned into two equal-sum subsets?
    
    This is a variant of subset sum where target = total_sum / 2.
    """
    
    @staticmethod
    def can_partition(arr: List[int]) -> bool:
        """
        Check if array can be partitioned into two equal subsets.
        
        >>> PartitionEqualSum.can_partition([1, 5, 11, 5])
        True
        >>> PartitionEqualSum.can_partition([1, 2, 3, 5])
        False
        """
        total = sum(arr)
        
        # Must be even
        if total % 2 != 0:
            return False
        
        target = total // 2
        return subset_sum_exists(arr, target)
    
    @staticmethod
    def find_partition(arr: List[int]) -> Optional[Tuple[List[int], List[int]]]:
        """
        Find the two partitions if possible.
        
        >>> result = PartitionEqualSum.find_partition([1, 5, 11, 5])
        >>> result is not None and sum(result[0]) == sum(result[1])
        True
        """
        total = sum(arr)
        
        if total % 2 != 0:
            return None
        
        target = total // 2
        subset1 = subset_sum_find_one(arr, target)
        
        if subset1 is None:
            return None
        
        # Build second subset
        subset1_set = set()
        remaining = list(arr)
        
        for x in subset1:
            remaining.remove(x)
        
        return (subset1, remaining)
```

## 8. Applications

### 8.1 Common Use Cases

- **Cryptography**: Subset sum forms basis of some encryption
- **Resource allocation**: Exact budget matching
- **Load balancing**: Partition jobs equally
- **Bin packing**: Related problems
- **Financial**: Portfolio with exact target value

### 8.2 Related Problems

| Problem | Description | Relation |
|---------|-------------|----------|
| 0/1 Knapsack | Max value with weight limit | Generalization |
| Partition | Split into equal halves | Special case |
| Coin Change | Min coins for amount | Unbounded variant |
| Perfect Sum | Count subsets with sum | Counting variant |

## 9. Real-World Software Engineering Applications

### 9.1 Production Example: Budget Allocator

```python
from typing import Dict, List, Optional, Tuple, Set
from dataclasses import dataclass, field
from enum import Enum
from decimal import Decimal, ROUND_HALF_UP


class Priority(Enum):
    CRITICAL = 4
    HIGH = 3
    MEDIUM = 2
    LOW = 1


@dataclass
class BudgetItem:
    """Represents a budget request item."""
    id: str
    name: str
    amount: int  # In cents to avoid float issues
    department: str
    priority: Priority = Priority.MEDIUM
    dependencies: Set[str] = field(default_factory=set)
    is_divisible: bool = False
    min_amount: int = 0  # Minimum if divisible
    
    def __hash__(self):
        return hash(self.id)


@dataclass
class AllocationResult:
    """Result of budget allocation."""
    allocated_items: List[BudgetItem]
    total_allocated: int
    remaining_budget: int
    unallocated_items: List[BudgetItem]
    allocation_rate: float


class BudgetAllocator:
    """
    Production budget allocator using subset sum.
    
    Allocates budget items to exactly match or maximize 
    use of available budget.
    """
    
    def __init__(self, budget: int):
        """
        Initialize with total budget (in cents).
        
        Args:
            budget: Total available budget in cents
        """
        self.budget = budget
    
    def allocate_exact(
        self,
        items: List[BudgetItem]
    ) -> Optional[AllocationResult]:
        """
        Find exact allocation matching budget.
        
        Returns None if exact match is impossible.
        """
        n = len(items)
        amounts = [item.amount for item in items]
        
        # DP to find subset
        dp = [[False] * (self.budget + 1) for _ in range(n + 1)]
        dp[0][0] = True
        
        for i in range(1, n + 1):
            for j in range(self.budget + 1):
                dp[i][j] = dp[i - 1][j]
                if j >= amounts[i - 1] and dp[i - 1][j - amounts[i - 1]]:
                    dp[i][j] = True
        
        if not dp[n][self.budget]:
            return None
        
        # Reconstruct
        allocated = []
        i, j = n, self.budget
        
        while i > 0 and j > 0:
            if dp[i][j] and not dp[i - 1][j]:
                allocated.append(items[i - 1])
                j -= amounts[i - 1]
            i -= 1
        
        unallocated = [item for item in items if item not in allocated]
        
        return AllocationResult(
            allocated_items=allocated,
            total_allocated=sum(item.amount for item in allocated),
            remaining_budget=0,
            unallocated_items=unallocated,
            allocation_rate=1.0
        )
    
    def allocate_maximize(
        self,
        items: List[BudgetItem]
    ) -> AllocationResult:
        """
        Maximize budget utilization without exceeding.
        
        Uses modified subset sum to find closest sum <= budget.
        """
        n = len(items)
        amounts = [item.amount for item in items]
        
        # dp[j] = True if sum j is achievable
        dp = [False] * (self.budget + 1)
        dp[0] = True
        
        # Track which items form each sum
        parent = [-1] * (self.budget + 1)
        item_at = [-1] * (self.budget + 1)
        
        for i, amount in enumerate(amounts):
            # Iterate backwards
            for j in range(self.budget, amount - 1, -1):
                if dp[j - amount] and not dp[j]:
                    dp[j] = True
                    parent[j] = j - amount
                    item_at[j] = i
        
        # Find maximum achievable sum
        max_sum = self.budget
        while max_sum >= 0 and not dp[max_sum]:
            max_sum -= 1
        
        if max_sum < 0:
            return AllocationResult(
                allocated_items=[],
                total_allocated=0,
                remaining_budget=self.budget,
                unallocated_items=items,
                allocation_rate=0.0
            )
        
        # Reconstruct allocated items
        allocated_indices = set()
        current = max_sum
        
        while current > 0 and item_at[current] >= 0:
            allocated_indices.add(item_at[current])
            current = parent[current]
        
        allocated = [items[i] for i in allocated_indices]
        unallocated = [items[i] for i in range(n) if i not in allocated_indices]
        
        return AllocationResult(
            allocated_items=allocated,
            total_allocated=max_sum,
            remaining_budget=self.budget - max_sum,
            unallocated_items=unallocated,
            allocation_rate=max_sum / self.budget if self.budget > 0 else 0
        )
    
    def allocate_with_priorities(
        self,
        items: List[BudgetItem]
    ) -> AllocationResult:
        """
        Allocate considering item priorities.
        
        First ensures critical items, then maximizes.
        """
        # Sort by priority (highest first)
        sorted_items = sorted(
            items,
            key=lambda x: (-x.priority.value, x.amount)
        )
        
        allocated = []
        remaining = self.budget
        
        # First pass: allocate critical items
        critical_items = [
            item for item in sorted_items 
            if item.priority == Priority.CRITICAL
        ]
        
        for item in critical_items:
            if item.amount <= remaining:
                allocated.append(item)
                remaining -= item.amount
        
        # Second pass: maximize remaining budget
        other_items = [
            item for item in sorted_items 
            if item not in allocated
        ]
        
        if remaining > 0 and other_items:
            sub_allocator = BudgetAllocator(remaining)
            sub_result = sub_allocator.allocate_maximize(other_items)
            allocated.extend(sub_result.allocated_items)
            remaining = sub_result.remaining_budget
        
        unallocated = [item for item in items if item not in allocated]
        total_allocated = sum(item.amount for item in allocated)
        
        return AllocationResult(
            allocated_items=allocated,
            total_allocated=total_allocated,
            remaining_budget=remaining,
            unallocated_items=unallocated,
            allocation_rate=total_allocated / self.budget if self.budget > 0 else 0
        )
    
    def allocate_with_dependencies(
        self,
        items: List[BudgetItem]
    ) -> AllocationResult:
        """
        Allocate respecting item dependencies.
        
        If A depends on B, B must be allocated for A to be included.
        """
        item_map = {item.id: item for item in items}
        
        # Topological sort to handle dependencies
        def get_all_dependencies(item: BudgetItem) -> Set[str]:
            deps = set()
            stack = [item.id]
            
            while stack:
                current_id = stack.pop()
                if current_id not in item_map:
                    continue
                    
                for dep_id in item_map[current_id].dependencies:
                    if dep_id not in deps:
                        deps.add(dep_id)
                        stack.append(dep_id)
            
            return deps
        
        # Build item groups (item + all its dependencies)
        groups: List[Tuple[Set[BudgetItem], int]] = []
        
        for item in items:
            group = {item}
            deps = get_all_dependencies(item)
            
            for dep_id in deps:
                if dep_id in item_map:
                    group.add(item_map[dep_id])
            
            group_cost = sum(i.amount for i in group)
            groups.append((group, group_cost))
        
        # Remove duplicate groups and sort by efficiency
        unique_groups = list({frozenset(g[0]): g for g in groups}.values())
        unique_groups.sort(key=lambda x: -len(x[0]) / x[1] if x[1] > 0 else 0)
        
        # Greedy selection
        allocated_ids: Set[str] = set()
        remaining = self.budget
        
        for group, cost in unique_groups:
            if cost <= remaining:
                # Check if this group adds new items
                new_items = {item for item in group if item.id not in allocated_ids}
                new_cost = sum(item.amount for item in new_items)
                
                if new_cost <= remaining:
                    for item in new_items:
                        allocated_ids.add(item.id)
                    remaining -= new_cost
        
        allocated = [item_map[id_] for id_ in allocated_ids]
        unallocated = [item for item in items if item.id not in allocated_ids]
        total_allocated = sum(item.amount for item in allocated)
        
        return AllocationResult(
            allocated_items=allocated,
            total_allocated=total_allocated,
            remaining_budget=remaining,
            unallocated_items=unallocated,
            allocation_rate=total_allocated / self.budget if self.budget > 0 else 0
        )


def format_amount(cents: int) -> str:
    """Format cents as dollar string."""
    return f"${cents / 100:,.2f}"


# Demo
if __name__ == "__main__":
    print("Subset Sum Demo")
    print("=" * 50)
    
    # Basic subset sum
    print("\n1. Basic Subset Sum:")
    arr = [3, 5, 6, 7]
    target = 14
    
    print(f"   Array: {arr}")
    print(f"   Target: {target}")
    print(f"   Exists: {subset_sum_exists(arr, target)}")
    print(f"   One solution: {subset_sum_find_one(arr, target)}")
    
    # All solutions
    print("\n2. All Solutions:")
    ss = SubsetSum([1, 2, 3, 4, 5])
    target = 5
    
    print(f"   Array: [1, 2, 3, 4, 5]")
    print(f"   Target: {target}")
    print(f"   Count: {ss.count_subsets(target)}")
    print(f"   Solutions: {ss.find_all(target)}")
    
    # Partition
    print("\n3. Equal Sum Partition:")
    arr = [1, 5, 11, 5]
    
    print(f"   Array: {arr}")
    print(f"   Can partition: {PartitionEqualSum.can_partition(arr)}")
    
    result = PartitionEqualSum.find_partition(arr)
    if result:
        print(f"   Partition 1: {result[0]}, sum={sum(result[0])}")
        print(f"   Partition 2: {result[1]}, sum={sum(result[1])}")
    
    # Budget allocator
    print("\n4. Budget Allocator:")
    budget = 10000  # $100.00
    allocator = BudgetAllocator(budget)
    
    items = [
        BudgetItem("IT01", "Server Upgrade", 3500, "IT", Priority.HIGH),
        BudgetItem("IT02", "Software License", 2000, "IT", Priority.MEDIUM),
        BudgetItem("HR01", "Training Program", 4500, "HR", Priority.MEDIUM),
        BudgetItem("MK01", "Marketing Campaign", 3000, "Marketing", Priority.LOW),
        BudgetItem("IT03", "Security Audit", 2500, "IT", Priority.CRITICAL),
    ]
    
    print(f"   Budget: {format_amount(budget)}")
    print(f"   Items:")
    for item in items:
        print(f"      - {item.name}: {format_amount(item.amount)} ({item.priority.name})")
    
    result = allocator.allocate_with_priorities(items)
    print(f"\n   Allocation Result:")
    print(f"   Allocated: {format_amount(result.total_allocated)}")
    print(f"   Remaining: {format_amount(result.remaining_budget)}")
    print(f"   Rate: {result.allocation_rate:.1%}")
    print(f"   Items allocated:")
    for item in result.allocated_items:
        print(f"      - {item.name}")
```

## 10. Comparison

| Approach | Time | Space | Best For |
|----------|------|-------|----------|
| Backtracking | O(2^n) | O(n) | Finding all solutions |
| DP | O(nS) | O(S) | Decision, small S |
| Meet Middle | O(2^(n/2)) | O(2^(n/2)) | Large n, any S |

## 11. Best Practices

1. **Choose algorithm based on input**:
   - Small n (< 20): Backtracking
   - Small S: DP
   - Large n, any S: Meet in the middle

2. **Optimize DP space** using 1D array

3. **Prune aggressively** in backtracking:
   - Upper bound (remaining sum)
   - Lower bound (sorted array)
   - Skip duplicates

4. **Use integers** to avoid floating point issues

5. **Handle edge cases**: empty array, target = 0

## 12. References

- [Wikipedia: Subset Sum Problem](https://en.wikipedia.org/wiki/Subset_sum_problem)
- [Wikipedia: Partition Problem](https://en.wikipedia.org/wiki/Partition_problem)
- Cormen, T. et al. "Introduction to Algorithms" - Dynamic Programming
