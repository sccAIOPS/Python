# Coin Change (Greedy Approach)

## Overview
- **Category**: Greedy Algorithm / Optimization
- **Complexity**: Time: O(n) per denomination | Space: O(1)
- **Type**: Minimum coin selection
- **Source Files**: [greedy_methods/minimum_coin_change.py](../../../greedy_methods/minimum_coin_change.py)

## 1. Mathematical Foundation

### 1.1 Problem Definition

Given coin denominations $d_1, d_2, ..., d_k$ and a target amount $A$:

**Objective**: Find minimum number of coins to make amount $A$

$$
\text{Minimize } \sum_{i=1}^{k} c_i
$$

**Subject to**:
$$
\sum_{i=1}^{k} c_i \cdot d_i = A, \quad c_i \geq 0, c_i \in \mathbb{Z}
$$

### 1.2 Greedy Strategy

Always pick the largest denomination that doesn't exceed remaining amount.

### 1.3 Canonical Coin Systems

A coin system is **canonical** if the greedy algorithm always gives optimal solution.

**Examples of canonical systems**:
- US coins: {1, 5, 10, 25}
- UK coins: {1, 2, 5, 10, 20, 50}
- Euro coins: {1, 2, 5, 10, 20, 50}
- Powers of 2: {1, 2, 4, 8, 16, ...}

**Condition**: For most practical systems, $d_i | d_{i+1}$ (divisibility) ensures canonicity.

### 1.4 Non-Canonical Counter-Example

Coins: {1, 3, 4}, Amount: 6

| Approach | Coins Used | Count |
|----------|------------|-------|
| Greedy | 4 + 1 + 1 | 3 |
| Optimal | 3 + 3 | 2 |

Greedy fails here!

### 1.5 When Greedy Works

Greedy is optimal when:
1. Each coin is a multiple of smaller coins
2. Denominations form a "standard" monetary system
3. The ratio between consecutive denominations is ≥ 2

### 1.6 Checking Canonicity

**Theorem** (Pearson): For $k$ denominations, if greedy fails, there exists a counter-example ≤ $d_k + d_{k-1}$.

Check amounts up to $d_k + d_{k-1}$ to verify canonicity.

## 2. Algorithm Variants

### 2.1 Greedy Coin Change
Always use largest possible coin.

### 2.2 Dynamic Programming
Guarantees optimal for any coin system.

### 2.3 BFS Approach
Find shortest path to target amount.

## 3. Pseudocode

### 3.1 Greedy Algorithm

```
ALGORITHM GreedyCoinChange(denominations, amount)
    INPUT: Coin denominations sorted descending, target amount
    OUTPUT: List of coins used, or None if impossible
    
    // Sort denominations in descending order
    Sort denominations descending
    
    coins_used ← []
    remaining ← amount
    
    for each coin in denominations do
        // Use as many of this coin as possible
        while remaining >= coin do
            coins_used.append(coin)
            remaining ← remaining - coin
    
    if remaining = 0 then
        return coins_used
    else
        return None  // Cannot make exact amount
```

### 3.2 Counting Coins

```
ALGORITHM CountCoinsGreedy(denominations, amount)
    INPUT: Coin denominations sorted descending, target amount
    OUTPUT: Dictionary of {denomination: count}
    
    Sort denominations descending
    
    coin_counts ← {}
    remaining ← amount
    
    for each coin in denominations do
        count ← remaining / coin  // Integer division
        if count > 0 then
            coin_counts[coin] ← count
            remaining ← remaining - (count × coin)
    
    if remaining = 0 then
        return coin_counts
    else
        return None
```

### 3.3 With Coin Availability Limit

```
ALGORITHM CoinChangeWithLimits(denominations, limits, amount)
    INPUT: Denominations, available count for each, target amount
    OUTPUT: Coins used or None
    
    Sort denominations descending (along with limits)
    
    coins_used ← []
    remaining ← amount
    
    for i ← 0 to length(denominations) - 1 do
        coin ← denominations[i]
        available ← limits[i]
        
        // Use up to available coins
        count ← min(remaining / coin, available)
        
        for j ← 1 to count do
            coins_used.append(coin)
        
        remaining ← remaining - (count × coin)
    
    if remaining = 0 then
        return coins_used
    else
        return None
```

## 4. Step-by-Step Example

### Example: US Coins, Amount = 67 cents

**Denominations**: {25, 10, 5, 1}

```
Step 1: remaining = 67
  Largest fitting: 25
  Use 25: remaining = 67 - 25 = 42
  Coins: [25]

Step 2: remaining = 42
  Largest fitting: 25
  Use 25: remaining = 42 - 25 = 17
  Coins: [25, 25]

Step 3: remaining = 17
  Largest fitting: 10
  Use 10: remaining = 17 - 10 = 7
  Coins: [25, 25, 10]

Step 4: remaining = 7
  Largest fitting: 5
  Use 5: remaining = 7 - 5 = 2
  Coins: [25, 25, 10, 5]

Step 5: remaining = 2
  Largest fitting: 1
  Use 1: remaining = 2 - 1 = 1
  Coins: [25, 25, 10, 5, 1]

Step 6: remaining = 1
  Use 1: remaining = 1 - 1 = 0
  Coins: [25, 25, 10, 5, 1, 1]

DONE! Total: 6 coins
```

### Visual Breakdown

```
67 cents decomposition:

25¢ │████████████████████████│  = 25
25¢ │████████████████████████│  = 25
10¢ │██████████│               = 10
 5¢ │█████│                    =  5
 1¢ │█│                        =  1
 1¢ │█│                        =  1
                               ─────
                                 67¢
```

## 5. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Sorting | O(k log k) | O(k) |
| Greedy selection | O(k) | O(1) |
| **Total** | O(k log k) | O(k) |

Where k = number of denominations.

**Note**: If denominations are pre-sorted, just O(k) time.

## 6. Visual Representation

### 6.1 Algorithm Flow

```
Amount: 67, Coins: [25, 10, 5, 1]

┌─────────────────────────────────────┐
│         Start: remaining = 67       │
└─────────────────┬───────────────────┘
                  │
                  ▼
┌─────────────────────────────────────┐
│    Try 25: 67 >= 25? YES            │
│    Use 2 quarters: 67 - 50 = 17     │
└─────────────────┬───────────────────┘
                  │
                  ▼
┌─────────────────────────────────────┐
│    Try 10: 17 >= 10? YES            │
│    Use 1 dime: 17 - 10 = 7          │
└─────────────────┬───────────────────┘
                  │
                  ▼
┌─────────────────────────────────────┐
│    Try 5: 7 >= 5? YES               │
│    Use 1 nickel: 7 - 5 = 2          │
└─────────────────┬───────────────────┘
                  │
                  ▼
┌─────────────────────────────────────┐
│    Try 1: 2 >= 1? YES               │
│    Use 2 pennies: 2 - 2 = 0         │
└─────────────────┬───────────────────┘
                  │
                  ▼
┌─────────────────────────────────────┐
│    Result: [25, 25, 10, 5, 1, 1]   │
│    Total: 6 coins                   │
└─────────────────────────────────────┘
```

### 6.2 Greedy Failure Case

```
Coins: [4, 3, 1], Amount: 6

Greedy approach:
  6 - 4 = 2  → use 4
  2 - 1 = 1  → use 1
  1 - 1 = 0  → use 1
  Result: [4, 1, 1] = 3 coins ❌

Optimal:
  6 - 3 = 3  → use 3
  3 - 3 = 0  → use 3
  Result: [3, 3] = 2 coins ✓

Greedy is NOT optimal for this system!
```

## 7. Implementation

```python
from typing import List, Dict, Optional, Tuple
from dataclasses import dataclass


def greedy_coin_change(
    denominations: List[int],
    amount: int
) -> Optional[List[int]]:
    """
    Greedy coin change algorithm.
    
    Works optimally for canonical coin systems (like US coins).
    
    >>> greedy_coin_change([25, 10, 5, 1], 67)
    [25, 25, 10, 5, 1, 1]
    >>> greedy_coin_change([25, 10, 5, 1], 30)
    [25, 5]
    >>> greedy_coin_change([5, 2], 3)  # Can't make 3
    """
    if amount < 0:
        return None
    if amount == 0:
        return []
    
    # Sort descending
    sorted_coins = sorted(denominations, reverse=True)
    
    result = []
    remaining = amount
    
    for coin in sorted_coins:
        while remaining >= coin:
            result.append(coin)
            remaining -= coin
    
    return result if remaining == 0 else None


def count_coins_greedy(
    denominations: List[int],
    amount: int
) -> Optional[Dict[int, int]]:
    """
    Count coins needed using greedy approach.
    
    >>> count_coins_greedy([25, 10, 5, 1], 67)
    {25: 2, 10: 1, 5: 1, 1: 2}
    """
    if amount < 0:
        return None
    if amount == 0:
        return {}
    
    sorted_coins = sorted(denominations, reverse=True)
    
    counts = {}
    remaining = amount
    
    for coin in sorted_coins:
        if remaining >= coin:
            count = remaining // coin
            counts[coin] = count
            remaining -= count * coin
    
    return counts if remaining == 0 else None


def min_coins_greedy(
    denominations: List[int],
    amount: int
) -> int:
    """
    Get minimum number of coins using greedy.
    
    Returns -1 if impossible.
    
    >>> min_coins_greedy([25, 10, 5, 1], 67)
    6
    >>> min_coins_greedy([5, 2], 3)
    -1
    """
    result = greedy_coin_change(denominations, amount)
    return len(result) if result is not None else -1


def coin_change_with_limits(
    denominations: List[int],
    limits: List[int],
    amount: int
) -> Optional[List[int]]:
    """
    Greedy coin change with limited coin availability.
    
    >>> coin_change_with_limits([25, 10, 5, 1], [1, 2, 2, 10], 50)
    [25, 10, 10, 5]
    """
    if amount < 0 or len(denominations) != len(limits):
        return None
    if amount == 0:
        return []
    
    # Sort by denomination (descending), keeping limits aligned
    paired = sorted(zip(denominations, limits), reverse=True)
    
    result = []
    remaining = amount
    
    for coin, limit in paired:
        count = min(remaining // coin, limit)
        result.extend([coin] * count)
        remaining -= count * coin
    
    return result if remaining == 0 else None


def is_canonical(denominations: List[int]) -> bool:
    """
    Check if coin system is canonical (greedy always optimal).
    
    Uses the Pearson bound: check amounts up to d_k + d_{k-1}.
    
    >>> is_canonical([25, 10, 5, 1])
    True
    >>> is_canonical([4, 3, 1])
    False
    """
    if not denominations or 1 not in denominations:
        return False  # Must have 1 to make all amounts
    
    sorted_coins = sorted(denominations, reverse=True)
    
    # Check up to sum of two largest
    if len(sorted_coins) >= 2:
        max_check = sorted_coins[0] + sorted_coins[1]
    else:
        max_check = sorted_coins[0] * 2
    
    for amount in range(1, max_check + 1):
        greedy = min_coins_greedy(sorted_coins, amount)
        optimal = _min_coins_dp(sorted_coins, amount)
        
        if greedy != optimal:
            return False
    
    return True


def _min_coins_dp(denominations: List[int], amount: int) -> int:
    """Helper: DP solution for comparison."""
    dp = [float('inf')] * (amount + 1)
    dp[0] = 0
    
    for coin in denominations:
        for i in range(coin, amount + 1):
            dp[i] = min(dp[i], dp[i - coin] + 1)
    
    return dp[amount] if dp[amount] != float('inf') else -1


@dataclass
class CoinChangeResult:
    """Detailed result of coin change."""
    amount: int
    coins: List[int]
    counts: Dict[int, int]
    total_coins: int
    is_optimal: bool
    
    def __str__(self) -> str:
        parts = [f"{count}×{coin}" for coin, count in sorted(
            self.counts.items(), reverse=True
        ) if count > 0]
        return f"{self.amount} = {' + '.join(parts)} ({self.total_coins} coins)"


class CoinChanger:
    """
    Coin change calculator with multiple features.
    """
    
    def __init__(self, denominations: List[int]):
        """
        Initialize with coin denominations.
        
        >>> cc = CoinChanger([25, 10, 5, 1])
        >>> cc.is_canonical
        True
        """
        self.denominations = sorted(denominations, reverse=True)
        self.is_canonical = is_canonical(denominations)
    
    def make_change(self, amount: int) -> Optional[CoinChangeResult]:
        """
        Make change for amount.
        
        >>> cc = CoinChanger([25, 10, 5, 1])
        >>> result = cc.make_change(67)
        >>> result.total_coins
        6
        """
        if amount < 0:
            return None
        if amount == 0:
            return CoinChangeResult(0, [], {}, 0, True)
        
        # Use greedy
        coins = greedy_coin_change(self.denominations, amount)
        
        if coins is None:
            return None
        
        counts = {}
        for coin in coins:
            counts[coin] = counts.get(coin, 0) + 1
        
        return CoinChangeResult(
            amount=amount,
            coins=coins,
            counts=counts,
            total_coins=len(coins),
            is_optimal=self.is_canonical
        )
    
    def make_change_optimal(self, amount: int) -> Optional[CoinChangeResult]:
        """
        Make change optimally (uses DP if needed).
        """
        if self.is_canonical:
            return self.make_change(amount)
        
        # Use DP for non-canonical systems
        return self._dp_change(amount)
    
    def _dp_change(self, amount: int) -> Optional[CoinChangeResult]:
        """DP-based coin change for non-canonical systems."""
        if amount < 0:
            return None
        if amount == 0:
            return CoinChangeResult(0, [], {}, 0, True)
        
        # DP with parent tracking
        dp = [float('inf')] * (amount + 1)
        parent = [-1] * (amount + 1)
        dp[0] = 0
        
        for i in range(1, amount + 1):
            for coin in self.denominations:
                if coin <= i and dp[i - coin] + 1 < dp[i]:
                    dp[i] = dp[i - coin] + 1
                    parent[i] = coin
        
        if dp[amount] == float('inf'):
            return None
        
        # Reconstruct
        coins = []
        current = amount
        while current > 0:
            coin = parent[current]
            coins.append(coin)
            current -= coin
        
        counts = {}
        for coin in coins:
            counts[coin] = counts.get(coin, 0) + 1
        
        return CoinChangeResult(
            amount=amount,
            coins=coins,
            counts=counts,
            total_coins=len(coins),
            is_optimal=True
        )
    
    def all_combinations(
        self,
        amount: int,
        max_results: int = 100
    ) -> List[Dict[int, int]]:
        """
        Find all ways to make change (not just minimum).
        
        >>> cc = CoinChanger([5, 2, 1])
        >>> combos = cc.all_combinations(5)
        >>> len(combos)
        4
        """
        results = []
        
        def backtrack(remaining: int, idx: int, current: Dict[int, int]):
            if remaining == 0:
                if len(results) < max_results:
                    results.append(current.copy())
                return
            
            if idx >= len(self.denominations):
                return
            
            coin = self.denominations[idx]
            max_use = remaining // coin
            
            for count in range(max_use, -1, -1):
                if count > 0:
                    current[coin] = count
                backtrack(remaining - count * coin, idx + 1, current)
                if count > 0:
                    del current[coin]
        
        backtrack(amount, 0, {})
        return results
```

## 8. Applications

### 8.1 Common Use Cases

- **Vending machines**: Give optimal change
- **Cash registers**: Minimize coin/bill usage
- **ATM dispensing**: Minimize notes dispensed
- **Currency exchange**: Efficient denomination selection
- **Game currencies**: In-game coin systems

### 8.2 Related Problems

| Problem | Description |
|---------|-------------|
| Coin Change (DP) | Minimum coins, any system |
| Coin Combinations | Count ways to make amount |
| Unbounded Knapsack | Generalized version |
| Stamp Problem | Similar with stamps |

## 9. Real-World Software Engineering Applications

### 9.1 Production Example: ATM Cash Dispenser

```python
from typing import Dict, List, Optional, Tuple
from dataclasses import dataclass, field
from datetime import datetime
from enum import Enum
import threading


class CashType(Enum):
    BILL = "bill"
    COIN = "coin"


@dataclass
class Denomination:
    """Represents a cash denomination."""
    value: int
    cash_type: CashType
    name: str
    count: int = 0
    
    @property
    def total_value(self) -> int:
        return self.value * self.count


@dataclass
class DispensingResult:
    """Result of cash dispensing operation."""
    success: bool
    amount_requested: int
    amount_dispensed: int
    denominations: Dict[int, int]
    error_message: Optional[str] = None
    transaction_id: Optional[str] = None


class CashInventory:
    """Thread-safe cash inventory management."""
    
    def __init__(self):
        self._lock = threading.RLock()
        self._denominations: Dict[int, Denomination] = {}
    
    def add_denomination(self, denom: Denomination):
        """Add or update denomination."""
        with self._lock:
            if denom.value in self._denominations:
                self._denominations[denom.value].count += denom.count
            else:
                self._denominations[denom.value] = denom
    
    def get_available(self, value: int) -> int:
        """Get available count for denomination."""
        with self._lock:
            if value in self._denominations:
                return self._denominations[value].count
            return 0
    
    def deduct(self, value: int, count: int) -> bool:
        """Deduct from inventory."""
        with self._lock:
            if value not in self._denominations:
                return False
            if self._denominations[value].count < count:
                return False
            self._denominations[value].count -= count
            return True
    
    def get_total_value(self) -> int:
        """Get total cash value in inventory."""
        with self._lock:
            return sum(d.total_value for d in self._denominations.values())
    
    def get_denominations(self) -> List[int]:
        """Get available denomination values."""
        with self._lock:
            return sorted(
                [v for v, d in self._denominations.items() if d.count > 0],
                reverse=True
            )


class ATMDispenser:
    """
    ATM cash dispenser using greedy algorithm.
    
    Optimizes for minimum number of bills dispensed.
    """
    
    def __init__(self):
        self.inventory = CashInventory()
        self._transaction_counter = 0
        self._lock = threading.Lock()
    
    def load_cash(self, denominations: List[Tuple[int, str, int]]):
        """
        Load cash into ATM.
        
        Args:
            denominations: List of (value, name, count)
        """
        for value, name, count in denominations:
            cash_type = CashType.BILL if value >= 100 else CashType.COIN
            denom = Denomination(value, cash_type, name, count)
            self.inventory.add_denomination(denom)
    
    def dispense(self, amount: int) -> DispensingResult:
        """
        Dispense requested amount.
        
        Uses greedy algorithm with inventory constraints.
        """
        with self._lock:
            self._transaction_counter += 1
            txn_id = f"TXN{self._transaction_counter:06d}"
        
        if amount <= 0:
            return DispensingResult(
                success=False,
                amount_requested=amount,
                amount_dispensed=0,
                denominations={},
                error_message="Invalid amount",
                transaction_id=txn_id
            )
        
        if amount > self.inventory.get_total_value():
            return DispensingResult(
                success=False,
                amount_requested=amount,
                amount_dispensed=0,
                denominations={},
                error_message="Insufficient funds in ATM",
                transaction_id=txn_id
            )
        
        # Try greedy dispensing
        result = self._greedy_dispense(amount)
        
        if result is None:
            # Try with backtracking for edge cases
            result = self._backtrack_dispense(amount)
        
        if result is None:
            return DispensingResult(
                success=False,
                amount_requested=amount,
                amount_dispensed=0,
                denominations={},
                error_message="Cannot dispense exact amount",
                transaction_id=txn_id
            )
        
        # Actually deduct from inventory
        for value, count in result.items():
            if not self.inventory.deduct(value, count):
                # Rollback
                return DispensingResult(
                    success=False,
                    amount_requested=amount,
                    amount_dispensed=0,
                    denominations={},
                    error_message="Inventory error during dispensing",
                    transaction_id=txn_id
                )
        
        return DispensingResult(
            success=True,
            amount_requested=amount,
            amount_dispensed=amount,
            denominations=result,
            transaction_id=txn_id
        )
    
    def _greedy_dispense(self, amount: int) -> Optional[Dict[int, int]]:
        """Greedy approach with inventory limits."""
        denominations = self.inventory.get_denominations()
        
        result = {}
        remaining = amount
        
        for denom in denominations:
            if remaining <= 0:
                break
            
            available = self.inventory.get_available(denom)
            needed = remaining // denom
            use = min(needed, available)
            
            if use > 0:
                result[denom] = use
                remaining -= use * denom
        
        return result if remaining == 0 else None
    
    def _backtrack_dispense(self, amount: int) -> Optional[Dict[int, int]]:
        """Backtracking for cases where greedy fails."""
        denominations = self.inventory.get_denominations()
        best_result = [None]
        best_count = [float('inf')]
        
        def backtrack(
            remaining: int,
            idx: int,
            current: Dict[int, int],
            total_count: int
        ):
            if remaining == 0:
                if total_count < best_count[0]:
                    best_count[0] = total_count
                    best_result[0] = current.copy()
                return
            
            if idx >= len(denominations):
                return
            
            if total_count >= best_count[0]:
                return  # Pruning
            
            denom = denominations[idx]
            available = self.inventory.get_available(denom)
            max_use = min(remaining // denom, available)
            
            for use in range(max_use, -1, -1):
                if use > 0:
                    current[denom] = use
                backtrack(
                    remaining - use * denom,
                    idx + 1,
                    current,
                    total_count + use
                )
                if use > 0:
                    del current[denom]
        
        backtrack(amount, 0, {}, 0)
        return best_result[0]
    
    def get_dispensable_amounts(
        self,
        max_amount: int = 1000
    ) -> List[int]:
        """Get all amounts that can be dispensed."""
        denominations = self.inventory.get_denominations()
        
        # DP to find all possible sums
        possible = {0}
        
        for denom in denominations:
            available = self.inventory.get_available(denom)
            new_possible = set()
            
            for existing in possible:
                for count in range(available + 1):
                    new_sum = existing + count * denom
                    if new_sum <= max_amount:
                        new_possible.add(new_sum)
            
            possible = possible.union(new_possible)
        
        return sorted(possible)


class CashRegister:
    """
    Cash register for point-of-sale systems.
    
    Calculates optimal change to give customers.
    """
    
    def __init__(self, denominations: List[int]):
        """
        Initialize with available denominations.
        
        Args:
            denominations: Available coin/bill values
        """
        self.denominations = sorted(denominations, reverse=True)
        self.changer = CoinChanger(denominations)
    
    def calculate_change(
        self,
        price: int,
        payment: int
    ) -> Optional[Dict[int, int]]:
        """
        Calculate change to give.
        
        Args:
            price: Item price in cents
            payment: Amount paid in cents
        
        Returns:
            Dictionary of {denomination: count}
        """
        change_due = payment - price
        
        if change_due < 0:
            return None  # Insufficient payment
        
        if change_due == 0:
            return {}
        
        result = self.changer.make_change_optimal(change_due)
        return result.counts if result else None
    
    def suggest_payment(
        self,
        price: int,
        customer_cash: Dict[int, int]
    ) -> Optional[int]:
        """
        Suggest optimal payment amount for easier change.
        
        Args:
            price: Item price
            customer_cash: What customer has {denomination: count}
        
        Returns:
            Suggested payment amount
        """
        # Find amounts customer can pay
        possible_payments = self._get_possible_amounts(customer_cash)
        
        # Find payment that minimizes change coins
        best_payment = None
        min_change_coins = float('inf')
        
        for payment in possible_payments:
            if payment < price:
                continue
            
            change = payment - price
            result = self.changer.make_change_optimal(change)
            
            if result and result.total_coins < min_change_coins:
                min_change_coins = result.total_coins
                best_payment = payment
        
        return best_payment
    
    def _get_possible_amounts(
        self,
        cash: Dict[int, int],
        max_amount: int = 10000
    ) -> List[int]:
        """Get all possible payment amounts."""
        possible = {0}
        
        for denom, count in cash.items():
            new_possible = set()
            for existing in possible:
                for c in range(count + 1):
                    new_sum = existing + c * denom
                    if new_sum <= max_amount:
                        new_possible.add(new_sum)
            possible = possible.union(new_possible)
        
        return sorted(possible)


# Demo
if __name__ == "__main__":
    print("Coin Change (Greedy) Demo")
    print("=" * 50)
    
    # Basic greedy
    print("\n1. Basic Greedy Coin Change:")
    coins = [25, 10, 5, 1]
    amount = 67
    
    result = greedy_coin_change(coins, amount)
    print(f"   Coins: {coins}")
    print(f"   Amount: {amount}")
    print(f"   Result: {result}")
    print(f"   Count: {len(result) if result else 'N/A'}")
    
    # Canonical check
    print("\n2. Canonical System Check:")
    systems = [
        ([25, 10, 5, 1], "US coins"),
        ([4, 3, 1], "Non-canonical"),
        ([1, 5, 10, 20, 50, 100], "Euro-like"),
    ]
    
    for denoms, name in systems:
        canonical = is_canonical(denoms)
        print(f"   {name} {denoms}: {'✓ canonical' if canonical else '✗ not canonical'}")
    
    # CoinChanger class
    print("\n3. CoinChanger with Details:")
    cc = CoinChanger([25, 10, 5, 1])
    result = cc.make_change(67)
    print(f"   {result}")
    
    # Non-canonical system
    print("\n4. Non-Canonical System (needs DP):")
    cc_nc = CoinChanger([4, 3, 1])
    
    greedy_result = cc_nc.make_change(6)
    optimal_result = cc_nc.make_change_optimal(6)
    
    print(f"   Coins: [4, 3, 1], Amount: 6")
    print(f"   Greedy: {greedy_result}")
    print(f"   Optimal: {optimal_result}")
    
    # ATM Dispenser
    print("\n5. ATM Dispenser:")
    atm = ATMDispenser()
    atm.load_cash([
        (10000, "$100 bill", 10),
        (5000, "$50 bill", 20),
        (2000, "$20 bill", 50),
        (1000, "$10 bill", 100),
    ])
    
    result = atm.dispense(18000)  # $180
    print(f"   Requesting: $180")
    print(f"   Success: {result.success}")
    print(f"   Dispensed: {result.denominations}")
    
    # Cash Register
    print("\n6. Cash Register:")
    register = CashRegister([100, 50, 25, 10, 5, 1])
    
    price = 347  # $3.47
    payment = 500  # $5.00
    
    change = register.calculate_change(price, payment)
    print(f"   Price: ${price/100:.2f}")
    print(f"   Payment: ${payment/100:.2f}")
    print(f"   Change: {change}")
```

## 10. Comparison

| Approach | Time | Optimal | Use Case |
|----------|------|---------|----------|
| Greedy | O(k) | Only canonical | Standard currencies |
| DP | O(n×A) | Always | Any coin system |
| BFS | O(A×k) | Always | Small amounts |

## 11. Best Practices

1. **Verify canonicity** before using greedy
2. **Sort denominations** once at initialization
3. **Handle edge cases**: zero, negative, impossible
4. **Consider limits**: available coin counts
5. **Use DP fallback** for non-canonical systems

## 12. References

- [Wikipedia: Change-making Problem](https://en.wikipedia.org/wiki/Change-making_problem)
- Pearson, D. "A Polynomial-time Algorithm for the Change-Making Problem"
- Cormen, T. et al. "Introduction to Algorithms" - Greedy Algorithms
