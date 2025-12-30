# Fractional Knapsack Problem

## Overview
- **Category**: Greedy Algorithm / Optimization
- **Complexity**: Time: O(n log n) | Space: O(n)
- **Type**: Continuous optimization
- **Source Files**: [greedy_methods/fractional_knapsack.py](../../../greedy_methods/fractional_knapsack.py)

## 1. Mathematical Foundation

### 1.1 Problem Definition

Given $n$ items with weights $w_1, ..., w_n$ and values $v_1, ..., v_n$, and a knapsack capacity $W$:

**Objective**: Maximize total value

$$
\text{Maximize } \sum_{i=1}^{n} x_i \cdot v_i
$$

**Subject to**:
$$
\sum_{i=1}^{n} x_i \cdot w_i \leq W, \quad 0 \leq x_i \leq 1
$$

Where $x_i$ is the fraction of item $i$ taken.

### 1.2 Value Density

**Value-to-weight ratio** (density):
$$
d_i = \frac{v_i}{w_i}
$$

### 1.3 Greedy Choice Property

**Theorem**: Taking items in decreasing order of density $d_i$ gives optimal solution.

**Proof**:
1. Assume optimal solution $X^*$ doesn't follow density order
2. Find items $i, j$ where $d_i > d_j$ but $x_j^* > 0$ and $x_i^* < 1$
3. Swap some fraction from $j$ to $i$
4. This increases total value, contradicting optimality

### 1.4 Comparison with 0/1 Knapsack

| Property | Fractional | 0/1 |
|----------|-----------|-----|
| Items | Can be split | All or nothing |
| Algorithm | Greedy | Dynamic Programming |
| Complexity | O(n log n) | O(nW) |
| Optimal | Yes | Yes |

### 1.5 Linear Programming Formulation

Fractional Knapsack is a **Linear Program**:
$$
\text{max } \mathbf{v}^T \mathbf{x}
$$
$$
\text{s.t. } \mathbf{w}^T \mathbf{x} \leq W, \quad \mathbf{0} \leq \mathbf{x} \leq \mathbf{1}
$$

This LP has at most one fractional variable in optimal solution.

## 2. Algorithm Steps

1. Calculate value density for each item
2. Sort items by density (descending)
3. Greedily take items:
   - If item fits completely, take all
   - If item doesn't fit, take fraction
4. Stop when knapsack is full

## 3. Pseudocode

### 3.1 Basic Algorithm

```
ALGORITHM FractionalKnapsack(items, capacity)
    INPUT: List of (weight, value) items, knapsack capacity W
    OUTPUT: Maximum value and fractions taken
    
    n ← length(items)
    
    // Calculate density and sort
    for i ← 1 to n do
        items[i].density ← items[i].value / items[i].weight
    
    Sort items by density in descending order
    
    total_value ← 0
    remaining_capacity ← capacity
    fractions ← array of size n, initialized to 0
    
    for i ← 1 to n do
        if remaining_capacity = 0 then
            break
        
        if items[i].weight <= remaining_capacity then
            // Take entire item
            fractions[i] ← 1
            total_value ← total_value + items[i].value
            remaining_capacity ← remaining_capacity - items[i].weight
        else
            // Take fraction of item
            fraction ← remaining_capacity / items[i].weight
            fractions[i] ← fraction
            total_value ← total_value + fraction × items[i].value
            remaining_capacity ← 0
    
    return (total_value, fractions)
```

### 3.2 With Original Indices

```
ALGORITHM FractionalKnapsackDetailed(items, capacity)
    INPUT: Items with (weight, value, id), capacity W
    OUTPUT: Solution with item selections
    
    // Augment with density
    augmented ← []
    for each item in items do
        augmented.append({
            item: item,
            density: item.value / item.weight
        })
    
    Sort augmented by density descending
    
    solution ← []
    remaining ← capacity
    total_value ← 0
    
    for each entry in augmented do
        if remaining = 0 then
            break
        
        item ← entry.item
        
        if item.weight <= remaining then
            fraction ← 1.0
            remaining ← remaining - item.weight
        else
            fraction ← remaining / item.weight
            remaining ← 0
        
        total_value ← total_value + fraction × item.value
        solution.append((item.id, fraction, fraction × item.value))
    
    return (total_value, solution)
```

## 4. Step-by-Step Example

### Example: Capacity = 50

| Item | Weight | Value | Density |
|------|--------|-------|---------|
| A | 10 | 60 | 6.0 |
| B | 20 | 100 | 5.0 |
| C | 30 | 120 | 4.0 |

**Step 1: Sort by density**
Order: A (6.0), B (5.0), C (4.0)

**Step 2: Greedy selection**
```
Remaining capacity: 50

Take A (w=10): Fits entirely
  Fraction: 1.0
  Value gained: 60
  Remaining: 50 - 10 = 40

Take B (w=20): Fits entirely
  Fraction: 1.0
  Value gained: 100
  Remaining: 40 - 20 = 20

Take C (w=30): Doesn't fit entirely!
  Fraction: 20/30 = 0.667
  Value gained: 120 × 0.667 = 80
  Remaining: 0
```

**Result**:
- Total value: 60 + 100 + 80 = **240**
- Items: A (100%), B (100%), C (66.7%)

### Visual Representation

```
Knapsack (Capacity = 50):
┌─────────────────────────────────────────────────────┐
│ A (10) │    B (20)    │    C (20 of 30)             │
│  $60   │    $100      │       $80                   │
└─────────────────────────────────────────────────────┘
     10        20                20            = 50

Remaining C (unused): 10 units, $40 value
```

## 5. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Calculate densities | O(n) | O(n) |
| Sort by density | O(n log n) | O(n) |
| Greedy selection | O(n) | O(1) |
| **Total** | O(n log n) | O(n) |

## 6. Visual Representation

### 6.1 Algorithm Flow

```
┌─────────────────────────────────────┐
│     Input: Items + Capacity         │
│  [(10,60), (20,100), (30,120)], 50 │
└─────────────────┬───────────────────┘
                  │
                  ▼
┌─────────────────────────────────────┐
│     Calculate Densities             │
│   A: 6.0, B: 5.0, C: 4.0           │
└─────────────────┬───────────────────┘
                  │
                  ▼
┌─────────────────────────────────────┐
│     Sort by Density (desc)          │
│        [A, B, C]                    │
└─────────────────┬───────────────────┘
                  │
                  ▼
┌─────────────────────────────────────┐
│     Greedy Selection                │
│   A: 1.0 → remaining: 40           │
│   B: 1.0 → remaining: 20           │
│   C: 0.67 → remaining: 0           │
└─────────────────┬───────────────────┘
                  │
                  ▼
┌─────────────────────────────────────┐
│     Result: Value = 240             │
│   Fractions: [1.0, 1.0, 0.67]      │
└─────────────────────────────────────┘
```

### 6.2 Density-Based Ordering

```
Value
  ↑
120 │           ┌─────────┐
    │           │    C    │  d = 4.0
100 │     ┌─────┤         │
    │     │  B  │         │  d = 5.0
 60 │ ┌───┤     │         │
    │ │ A │     │         │  d = 6.0
    │ └───┴─────┴─────────┘
    └──────────────────────→ Weight
       10   20     30

Greedy: Take in order A → B → C (partial)
```

## 7. Implementation

```python
from typing import List, Tuple, NamedTuple, Optional
from dataclasses import dataclass
from decimal import Decimal


@dataclass
class Item:
    """Item for knapsack problem."""
    id: str
    weight: float
    value: float
    
    @property
    def density(self) -> float:
        """Value-to-weight ratio."""
        if self.weight == 0:
            return float('inf') if self.value > 0 else 0
        return self.value / self.weight


class Selection(NamedTuple):
    """Selection result for an item."""
    item_id: str
    fraction: float
    weight_taken: float
    value_gained: float


@dataclass
class KnapsackResult:
    """Result of knapsack optimization."""
    total_value: float
    total_weight: float
    selections: List[Selection]
    remaining_capacity: float


def fractional_knapsack(
    items: List[Tuple[float, float]],
    capacity: float
) -> Tuple[float, List[float]]:
    """
    Solve fractional knapsack problem.
    
    Args:
        items: List of (weight, value) tuples
        capacity: Knapsack capacity
    
    Returns:
        Tuple of (max_value, fractions)
    
    >>> items = [(10, 60), (20, 100), (30, 120)]
    >>> value, fractions = fractional_knapsack(items, 50)
    >>> round(value, 2)
    240.0
    >>> [round(f, 2) for f in fractions]
    [1.0, 1.0, 0.67]
    """
    if not items or capacity <= 0:
        return (0.0, [0.0] * len(items))
    
    n = len(items)
    
    # Calculate densities with original indices
    indexed = [
        (i, items[i][0], items[i][1], items[i][1] / items[i][0] if items[i][0] > 0 else float('inf'))
        for i in range(n)
    ]
    
    # Sort by density (descending)
    indexed.sort(key=lambda x: -x[3])
    
    fractions = [0.0] * n
    total_value = 0.0
    remaining = capacity
    
    for idx, weight, value, density in indexed:
        if remaining <= 0:
            break
        
        if weight <= remaining:
            fractions[idx] = 1.0
            total_value += value
            remaining -= weight
        else:
            fraction = remaining / weight
            fractions[idx] = fraction
            total_value += fraction * value
            remaining = 0
    
    return (total_value, fractions)


def fractional_knapsack_detailed(
    items: List[Item],
    capacity: float
) -> KnapsackResult:
    """
    Solve fractional knapsack with detailed results.
    
    >>> items = [
    ...     Item("A", 10, 60),
    ...     Item("B", 20, 100),
    ...     Item("C", 30, 120)
    ... ]
    >>> result = fractional_knapsack_detailed(items, 50)
    >>> round(result.total_value, 2)
    240.0
    """
    if not items or capacity <= 0:
        return KnapsackResult(0, 0, [], capacity)
    
    # Sort by density
    sorted_items = sorted(items, key=lambda x: -x.density)
    
    selections = []
    total_value = 0.0
    total_weight = 0.0
    remaining = capacity
    
    for item in sorted_items:
        if remaining <= 0:
            break
        
        if item.weight <= remaining:
            fraction = 1.0
            weight_taken = item.weight
            value_gained = item.value
        else:
            fraction = remaining / item.weight
            weight_taken = remaining
            value_gained = fraction * item.value
        
        selections.append(Selection(
            item_id=item.id,
            fraction=fraction,
            weight_taken=weight_taken,
            value_gained=value_gained
        ))
        
        total_value += value_gained
        total_weight += weight_taken
        remaining -= weight_taken
    
    return KnapsackResult(
        total_value=total_value,
        total_weight=total_weight,
        selections=selections,
        remaining_capacity=remaining
    )


class FractionalKnapsack:
    """
    Fractional Knapsack solver with various options.
    """
    
    def __init__(self, capacity: float):
        """
        Initialize with capacity.
        
        >>> fk = FractionalKnapsack(50)
        >>> fk.capacity
        50
        """
        self.capacity = capacity
        self.items: List[Item] = []
    
    def add_item(self, id: str, weight: float, value: float):
        """Add item to consideration."""
        self.items.append(Item(id, weight, value))
    
    def solve(self) -> KnapsackResult:
        """Solve the knapsack problem."""
        return fractional_knapsack_detailed(self.items, self.capacity)
    
    def solve_with_constraints(
        self,
        min_fractions: Optional[dict] = None,
        max_fractions: Optional[dict] = None
    ) -> KnapsackResult:
        """
        Solve with minimum/maximum fraction constraints.
        
        Args:
            min_fractions: {item_id: min_fraction}
            max_fractions: {item_id: max_fraction}
        """
        min_fractions = min_fractions or {}
        max_fractions = max_fractions or {}
        
        # First, fulfill minimum requirements
        remaining = self.capacity
        forced_selections = []
        
        for item in self.items:
            min_f = min_fractions.get(item.id, 0)
            if min_f > 0:
                weight_needed = min_f * item.weight
                if weight_needed > remaining:
                    # Can't satisfy constraint
                    return KnapsackResult(0, 0, [], self.capacity)
                
                forced_selections.append(Selection(
                    item_id=item.id,
                    fraction=min_f,
                    weight_taken=weight_needed,
                    value_gained=min_f * item.value
                ))
                remaining -= weight_needed
        
        # Build remaining items with adjusted capacities
        remaining_items = []
        for item in self.items:
            min_f = min_fractions.get(item.id, 0)
            max_f = max_fractions.get(item.id, 1.0)
            available_fraction = max_f - min_f
            
            if available_fraction > 0:
                remaining_items.append(Item(
                    id=item.id,
                    weight=available_fraction * item.weight,
                    value=available_fraction * item.value
                ))
        
        # Solve for remaining capacity
        remaining_result = fractional_knapsack_detailed(remaining_items, remaining)
        
        # Combine results
        all_selections = forced_selections + [
            Selection(
                item_id=s.item_id,
                fraction=min_fractions.get(s.item_id, 0) + s.fraction * (
                    max_fractions.get(s.item_id, 1.0) - min_fractions.get(s.item_id, 0)
                ),
                weight_taken=s.weight_taken,
                value_gained=s.value_gained
            )
            for s in remaining_result.selections
        ]
        
        total_value = sum(s.value_gained for s in all_selections)
        total_weight = sum(s.weight_taken for s in all_selections)
        
        return KnapsackResult(
            total_value=total_value,
            total_weight=total_weight,
            selections=all_selections,
            remaining_capacity=self.capacity - total_weight
        )
    
    def analyze_sensitivity(self) -> dict:
        """
        Analyze sensitivity of solution to capacity changes.
        
        Returns marginal value per unit capacity.
        """
        result = self.solve()
        
        # Find the marginal item (partially taken or first not taken)
        sorted_items = sorted(self.items, key=lambda x: -x.density)
        
        marginal_density = 0
        for item in sorted_items:
            selection = next(
                (s for s in result.selections if s.item_id == item.id),
                None
            )
            if selection is None or selection.fraction < 1.0:
                marginal_density = item.density
                break
        
        return {
            'current_value': result.total_value,
            'marginal_density': marginal_density,
            'capacity_utilization': result.total_weight / self.capacity
        }
```

## 8. Applications

### 8.1 Common Use Cases

- **Resource allocation**: Budget distribution
- **Cargo loading**: Maximize cargo value
- **Investment**: Portfolio optimization
- **Bandwidth allocation**: Network resource sharing
- **Cloud computing**: Resource provisioning

### 8.2 Related Problems

| Problem | Description |
|---------|-------------|
| 0/1 Knapsack | Items cannot be split |
| Bounded Knapsack | Limited copies of each item |
| Unbounded Knapsack | Unlimited copies |
| Multi-Knapsack | Multiple knapsacks |

## 9. Real-World Software Engineering Applications

### 9.1 Production Example: Cloud Resource Allocator

```python
from typing import Dict, List, Optional, Tuple
from dataclasses import dataclass, field
from enum import Enum
from datetime import datetime, timedelta
import math


class ResourceType(Enum):
    CPU = "cpu"
    MEMORY = "memory"
    GPU = "gpu"
    STORAGE = "storage"


@dataclass
class ResourceRequest:
    """Request for cloud resources."""
    id: str
    customer: str
    resource_type: ResourceType
    amount: float  # Units requested
    value: float  # Revenue per unit-hour
    priority: int = 1
    min_allocation: float = 0  # Minimum fraction needed
    preemptible: bool = True


@dataclass
class AllocationResult:
    """Result of resource allocation."""
    allocations: Dict[str, float]  # request_id -> fraction
    total_revenue: float
    utilization: float
    unmet_demand: float


class CloudResourceAllocator:
    """
    Allocate cloud resources using fractional knapsack.
    
    Maximizes revenue while respecting capacity constraints.
    """
    
    def __init__(
        self,
        capacity: Dict[ResourceType, float]
    ):
        """
        Initialize with resource capacities.
        
        Args:
            capacity: {ResourceType: available_units}
        """
        self.capacity = capacity
    
    def allocate(
        self,
        requests: List[ResourceRequest]
    ) -> Dict[ResourceType, AllocationResult]:
        """
        Allocate resources to requests.
        
        Uses fractional knapsack per resource type.
        """
        # Group requests by resource type
        by_type: Dict[ResourceType, List[ResourceRequest]] = {}
        for req in requests:
            if req.resource_type not in by_type:
                by_type[req.resource_type] = []
            by_type[req.resource_type].append(req)
        
        results = {}
        
        for res_type, type_requests in by_type.items():
            cap = self.capacity.get(res_type, 0)
            results[res_type] = self._allocate_single_type(
                type_requests, cap
            )
        
        return results
    
    def _allocate_single_type(
        self,
        requests: List[ResourceRequest],
        capacity: float
    ) -> AllocationResult:
        """Allocate single resource type."""
        if not requests or capacity <= 0:
            return AllocationResult({}, 0, 0, 0)
        
        # Sort by value density (value per unit)
        sorted_reqs = sorted(
            requests,
            key=lambda r: (-r.priority, -r.value)
        )
        
        allocations = {}
        total_revenue = 0.0
        remaining = capacity
        
        for req in sorted_reqs:
            if remaining <= 0:
                allocations[req.id] = 0
                continue
            
            if req.amount <= remaining:
                # Full allocation
                allocations[req.id] = 1.0
                total_revenue += req.amount * req.value
                remaining -= req.amount
            elif remaining >= req.amount * req.min_allocation:
                # Partial allocation if above minimum
                fraction = remaining / req.amount
                allocations[req.id] = fraction
                total_revenue += remaining * req.value
                remaining = 0
            else:
                # Can't meet minimum requirement
                allocations[req.id] = 0
        
        total_demand = sum(r.amount for r in requests)
        total_allocated = capacity - remaining
        
        return AllocationResult(
            allocations=allocations,
            total_revenue=total_revenue,
            utilization=total_allocated / capacity if capacity > 0 else 0,
            unmet_demand=total_demand - total_allocated
        )
    
    def allocate_with_sla(
        self,
        requests: List[ResourceRequest],
        sla_minimums: Dict[str, float]  # customer -> min_allocation
    ) -> Dict[ResourceType, AllocationResult]:
        """
        Allocate with SLA guarantees.
        
        Ensures minimum allocations for certain customers.
        """
        # First pass: satisfy SLA requirements
        sla_allocations: Dict[ResourceType, Dict[str, float]] = {}
        remaining_capacity = dict(self.capacity)
        
        for req in requests:
            customer_min = sla_minimums.get(req.customer, 0)
            if customer_min > 0:
                res_type = req.resource_type
                if res_type not in sla_allocations:
                    sla_allocations[res_type] = {}
                
                required = req.amount * customer_min
                if required <= remaining_capacity.get(res_type, 0):
                    sla_allocations[res_type][req.id] = customer_min
                    remaining_capacity[res_type] -= required
        
        # Second pass: allocate remaining capacity
        remaining_requests = []
        for req in requests:
            res_type = req.resource_type
            sla_fraction = sla_allocations.get(res_type, {}).get(req.id, 0)
            
            if sla_fraction < 1.0:
                # Create adjusted request for remaining portion
                remaining_req = ResourceRequest(
                    id=req.id,
                    customer=req.customer,
                    resource_type=req.resource_type,
                    amount=req.amount * (1 - sla_fraction),
                    value=req.value,
                    priority=req.priority,
                    min_allocation=0,
                    preemptible=req.preemptible
                )
                remaining_requests.append(remaining_req)
        
        # Use remaining capacity
        temp_allocator = CloudResourceAllocator(remaining_capacity)
        additional = temp_allocator.allocate(remaining_requests)
        
        # Combine results
        final_results = {}
        for res_type in self.capacity:
            sla_allocs = sla_allocations.get(res_type, {})
            add_result = additional.get(res_type, AllocationResult({}, 0, 0, 0))
            
            combined_allocs = {}
            for req in requests:
                if req.resource_type == res_type:
                    sla = sla_allocs.get(req.id, 0)
                    add = add_result.allocations.get(req.id, 0)
                    combined_allocs[req.id] = sla + add * (1 - sla)
            
            total_revenue = sum(
                combined_allocs.get(r.id, 0) * r.amount * r.value
                for r in requests if r.resource_type == res_type
            )
            
            total_allocated = sum(
                combined_allocs.get(r.id, 0) * r.amount
                for r in requests if r.resource_type == res_type
            )
            
            cap = self.capacity[res_type]
            total_demand = sum(
                r.amount for r in requests if r.resource_type == res_type
            )
            
            final_results[res_type] = AllocationResult(
                allocations=combined_allocs,
                total_revenue=total_revenue,
                utilization=total_allocated / cap if cap > 0 else 0,
                unmet_demand=total_demand - total_allocated
            )
        
        return final_results
    
    def optimize_pricing(
        self,
        requests: List[ResourceRequest],
        target_utilization: float = 0.9
    ) -> Dict[ResourceType, float]:
        """
        Suggest pricing multipliers to achieve target utilization.
        
        Returns multiplier per resource type.
        """
        results = self.allocate(requests)
        
        multipliers = {}
        for res_type, result in results.items():
            if result.utilization < target_utilization:
                # Lower prices to attract more demand
                multipliers[res_type] = 0.9
            elif result.utilization > 0.95:
                # Raise prices due to high demand
                multipliers[res_type] = 1.2
            else:
                multipliers[res_type] = 1.0
        
        return multipliers


class InvestmentPortfolioOptimizer:
    """
    Optimize investment portfolio using fractional knapsack.
    
    Unlike stocks, some assets (like bonds, funds) can be
    partially purchased.
    """
    
    def __init__(self, budget: float):
        self.budget = budget
        self.assets: List[Item] = []
    
    def add_asset(
        self,
        id: str,
        min_investment: float,
        max_investment: float,
        expected_return: float
    ):
        """
        Add investment asset.
        
        Args:
            id: Asset identifier
            min_investment: Minimum investment amount
            max_investment: Maximum investment amount
            expected_return: Expected return rate (e.g., 0.08 for 8%)
        """
        # Model as knapsack item
        # Weight = investment amount
        # Value = expected profit
        self.assets.append(Item(
            id=id,
            weight=max_investment,
            value=max_investment * expected_return
        ))
    
    def optimize(
        self,
        risk_weights: Optional[Dict[str, float]] = None
    ) -> KnapsackResult:
        """
        Optimize portfolio allocation.
        
        Args:
            risk_weights: Optional risk adjustment per asset
        """
        if not self.assets:
            return KnapsackResult(0, 0, [], self.budget)
        
        # Adjust values by risk if provided
        adjusted_assets = self.assets.copy()
        if risk_weights:
            adjusted_assets = [
                Item(
                    id=a.id,
                    weight=a.weight,
                    value=a.value * risk_weights.get(a.id, 1.0)
                )
                for a in self.assets
            ]
        
        return fractional_knapsack_detailed(adjusted_assets, self.budget)


# Demo
if __name__ == "__main__":
    print("Fractional Knapsack Demo")
    print("=" * 50)
    
    # Basic example
    print("\n1. Basic Fractional Knapsack:")
    items = [(10, 60), (20, 100), (30, 120)]
    capacity = 50
    
    value, fractions = fractional_knapsack(items, capacity)
    print(f"   Items (weight, value): {items}")
    print(f"   Capacity: {capacity}")
    print(f"   Maximum value: {value}")
    print(f"   Fractions: {[round(f, 2) for f in fractions]}")
    
    # Detailed result
    print("\n2. Detailed Solution:")
    items_detailed = [
        Item("A", 10, 60),
        Item("B", 20, 100),
        Item("C", 30, 120)
    ]
    
    result = fractional_knapsack_detailed(items_detailed, 50)
    print(f"   Total value: {result.total_value}")
    print(f"   Total weight: {result.total_weight}")
    print(f"   Selections:")
    for sel in result.selections:
        print(f"      {sel.item_id}: {sel.fraction:.1%} → ${sel.value_gained:.2f}")
    
    # Cloud resource allocator
    print("\n3. Cloud Resource Allocator:")
    allocator = CloudResourceAllocator({
        ResourceType.CPU: 100,  # 100 CPU cores
        ResourceType.MEMORY: 256,  # 256 GB RAM
    })
    
    requests = [
        ResourceRequest("R1", "CustomerA", ResourceType.CPU, 30, 10),
        ResourceRequest("R2", "CustomerA", ResourceType.CPU, 40, 8),
        ResourceRequest("R3", "CustomerB", ResourceType.CPU, 50, 12),
        ResourceRequest("R4", "CustomerB", ResourceType.MEMORY, 100, 5),
        ResourceRequest("R5", "CustomerC", ResourceType.MEMORY, 200, 4),
    ]
    
    results = allocator.allocate(requests)
    
    for res_type, result in results.items():
        print(f"\n   {res_type.value}:")
        print(f"      Revenue: ${result.total_revenue:.2f}")
        print(f"      Utilization: {result.utilization:.1%}")
        for req_id, fraction in result.allocations.items():
            if fraction > 0:
                print(f"      {req_id}: {fraction:.1%}")
    
    # Investment optimizer
    print("\n4. Investment Portfolio:")
    optimizer = InvestmentPortfolioOptimizer(10000)
    
    optimizer.add_asset("Bonds", 1000, 5000, 0.05)
    optimizer.add_asset("Index Fund", 500, 4000, 0.08)
    optimizer.add_asset("Growth Stock", 1000, 6000, 0.12)
    
    portfolio = optimizer.optimize()
    print(f"   Budget: $10,000")
    print(f"   Expected return: ${portfolio.total_value:.2f}")
    print(f"   Allocations:")
    for sel in portfolio.selections:
        investment = sel.weight_taken
        print(f"      {sel.item_id}: ${investment:.2f} ({sel.fraction:.1%})")
```

## 10. Comparison with 0/1 Knapsack

| Aspect | Fractional | 0/1 |
|--------|-----------|-----|
| Solution | Always exists | May not exist |
| Value | ≥ 0/1 optimal | ≤ Fractional optimal |
| Algorithm | Greedy | DP |
| Time | O(n log n) | O(nW) |

## 11. Best Practices

1. **Sort once** at the beginning
2. **Handle zero-weight** items carefully
3. **Use stable sort** if tie-breaking matters
4. **Consider precision** for floating point
5. **Validate input** (positive weights/values)

## 12. References

- [Wikipedia: Continuous Knapsack Problem](https://en.wikipedia.org/wiki/Continuous_knapsack_problem)
- [Wikipedia: Knapsack Problem](https://en.wikipedia.org/wiki/Knapsack_problem)
- Cormen, T. et al. "Introduction to Algorithms" - Greedy Algorithms
