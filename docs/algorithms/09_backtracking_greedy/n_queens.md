# N-Queens Problem

## Overview
- **Category**: Backtracking / Constraint Satisfaction
- **Complexity**: Time: O(N!) | Space: O(N²)
- **Type**: Combinatorial optimization
- **Source Files**: [backtracking/n_queens.py](../../../backtracking/n_queens.py)

## 1. Mathematical Foundation

### 1.1 Problem Definition

Place N queens on an N×N chessboard such that no two queens threaten each other.

**Constraints**:
- No two queens in same row
- No two queens in same column
- No two queens on same diagonal

### 1.2 Solution Count

| N | Solutions | Distinct (after rotations/reflections) |
|---|-----------|----------------------------------------|
| 1 | 1 | 1 |
| 4 | 2 | 1 |
| 5 | 10 | 2 |
| 6 | 4 | 1 |
| 7 | 40 | 6 |
| 8 | 92 | 12 |
| 9 | 352 | 46 |
| 10 | 724 | 92 |

### 1.3 Diagonal Formulas

For position $(r, c)$ on the board:

**Main diagonal** (top-left to bottom-right):
$$
\text{diag}_1 = r - c + (N - 1)
$$

**Anti-diagonal** (top-right to bottom-left):
$$
\text{diag}_2 = r + c
$$

Two queens $(r_1, c_1)$ and $(r_2, c_2)$ attack diagonally if:
$$
|r_1 - r_2| = |c_1 - c_2|
$$

### 1.4 State Space

**Total configurations**: $\binom{N^2}{N} = \frac{(N^2)!}{N!(N^2-N)!}$

**With row constraint**: $N^N$ (one queen per row)

**With row+column**: $N!$ (permutations)

### 1.5 Symmetry

A solution can be transformed by:
- 90°, 180°, 270° rotations
- Horizontal/vertical reflections
- Diagonal reflections

8 symmetry operations (dihedral group D₄)

## 2. Pseudocode

### 2.1 Basic Backtracking

```
ALGORITHM NQueens(N)
    INPUT: Board size N
    OUTPUT: All valid placements
    
    solutions ← []
    board ← empty N×N board
    
    Solve(board, 0, N, solutions)
    
    return solutions

ALGORITHM Solve(board, row, N, solutions)
    INPUT: Current board, current row, size, solutions list
    
    if row = N then
        // All queens placed successfully
        solutions.append(copy(board))
        return
    
    for col ← 0 to N-1 do
        if IsSafe(board, row, col, N) then
            board[row][col] ← 1  // Place queen
            Solve(board, row + 1, N, solutions)
            board[row][col] ← 0  // Remove queen (backtrack)

ALGORITHM IsSafe(board, row, col, N)
    INPUT: Board, position to check
    OUTPUT: True if safe, False otherwise
    
    // Check column above
    for i ← 0 to row-1 do
        if board[i][col] = 1 then
            return False
    
    // Check upper-left diagonal
    i, j ← row - 1, col - 1
    while i ≥ 0 AND j ≥ 0 do
        if board[i][j] = 1 then
            return False
        i, j ← i - 1, j - 1
    
    // Check upper-right diagonal
    i, j ← row - 1, col + 1
    while i ≥ 0 AND j < N do
        if board[i][j] = 1 then
            return False
        i, j ← i - 1, j + 1
    
    return True
```

### 2.2 Optimized with Sets

```
ALGORITHM NQueensOptimized(N)
    INPUT: Board size N
    OUTPUT: Number of solutions
    
    count ← 0
    cols ← {}        // Columns with queens
    diag1 ← {}       // Main diagonals
    diag2 ← {}       // Anti-diagonals
    
    SolveOptimized(0, N, cols, diag1, diag2, count)
    
    return count

ALGORITHM SolveOptimized(row, N, cols, diag1, diag2, count)
    if row = N then
        count ← count + 1
        return
    
    for col ← 0 to N-1 do
        d1 ← row - col + N - 1
        d2 ← row + col
        
        if col ∉ cols AND d1 ∉ diag1 AND d2 ∉ diag2 then
            cols.add(col)
            diag1.add(d1)
            diag2.add(d2)
            
            SolveOptimized(row + 1, N, cols, diag1, diag2, count)
            
            cols.remove(col)
            diag1.remove(d1)
            diag2.remove(d2)
```

### 2.3 Bitmask Optimization

```
ALGORITHM NQueensBitmask(N)
    INPUT: Board size N
    OUTPUT: Number of solutions
    
    count ← 0
    allOnes ← (1 << N) - 1  // N bits set to 1
    
    SolveBitmask(0, 0, 0, allOnes, count)
    
    return count

ALGORITHM SolveBitmask(cols, diag1, diag2, allOnes, count)
    if cols = allOnes then
        // All columns filled = all queens placed
        count ← count + 1
        return
    
    // Available positions
    available ← allOnes AND NOT(cols OR diag1 OR diag2)
    
    while available ≠ 0 do
        // Get rightmost available position
        pos ← available AND (-available)
        available ← available - pos
        
        // Place queen and recurse
        SolveBitmask(
            cols OR pos,
            (diag1 OR pos) << 1,
            (diag2 OR pos) >> 1,
            allOnes,
            count
        )
```

## 3. Step-by-Step Example

### 4-Queens Solution

```
Initial empty board:
. . . .
. . . .
. . . .
. . . .

Step 1: Place queen at (0, 1)
. Q . .
. . . .
. . . .
. . . .

Step 2: Try (1, 0) - Safe? No (diagonal)
        Try (1, 1) - Safe? No (column)
        Try (1, 2) - Safe? No (diagonal)
        Try (1, 3) - Safe? Yes
. Q . .
. . . Q
. . . .
. . . .

Step 3: Try row 2
        (2, 0) - Safe? Yes
. Q . .
. . . Q
Q . . .
. . . .

Step 4: Try row 3
        (3, 0) - Safe? No (column)
        (3, 1) - Safe? No (column)
        (3, 2) - Safe? No (diagonal from 1,3)
        (3, 3) - Safe? No (column)
        BACKTRACK!

Step 5: Remove queen from (2, 0), try (2, 1) - No (column)
        Try (2, 2) - Safe? No (diagonal from 1,3)
        No valid position - BACKTRACK to row 1

... continue until finding:

Solution 1:        Solution 2:
. Q . .            . . Q .
. . . Q            Q . . .
Q . . .            . . . Q
. . Q .            . Q . .
```

## 4. Complexity Analysis

| Approach | Time | Space |
|----------|------|-------|
| Brute force | O(N^N) | O(N²) |
| Basic backtracking | O(N!) | O(N²) |
| Optimized (sets) | O(N!) | O(N) |
| Bitmask | O(N!) | O(N) |

**Branching factor**: Decreases as we place queens
- Row 0: up to N choices
- Row 1: ~N-2 choices
- ...
- Average: ~N!/2

## 5. Visual Representation

### 5.1 Attack Patterns

```
    Q attacks:
    
    ↖ ↑ ↗       Column: ↑ ↓
     \|/        
    ← Q →       Row: ← →
     /|\        
    ↙ ↓ ↘       Diagonals: ↖↗ ↙↘
```

### 5.2 8-Queens Solution Tree (partial)

```
                        Start
                       /  |  \
                      Q   Q   Q  ...
                     (0) (1) (2)
                    / | \
                   .  Q  .     Valid positions for row 1
                     (3)
                    / | \
                   Q  .  .     Valid positions for row 2
                  (1)
                   |
                  ...
                   |
               Solution!
```

### 5.3 Diagonal Indexing

```
Main diagonals (r - c + N - 1):

  0 1 2 3  ← columns
0 [3 4 5 6]   Diagonal indices
1 [2 3 4 5]   for N=4
2 [1 2 3 4]
3 [0 1 2 3]
↑
rows

Anti-diagonals (r + c):

  0 1 2 3
0 [0 1 2 3]
1 [1 2 3 4]
2 [2 3 4 5]
3 [3 4 5 6]
```

## 6. Implementation

```python
from typing import List, Set, Tuple, Optional


class NQueens:
    """
    N-Queens problem solver using backtracking.
    
    Places N queens on NxN chessboard such that no two queens
    attack each other.
    """
    
    def __init__(self, n: int):
        """
        Initialize solver.
        
        >>> nq = NQueens(4)
        >>> nq.n
        4
        """
        self.n = n
        self.solutions: List[List[List[int]]] = []
    
    def solve(self) -> List[List[List[int]]]:
        """
        Find all solutions.
        
        >>> nq = NQueens(4)
        >>> solutions = nq.solve()
        >>> len(solutions)
        2
        """
        self.solutions = []
        board = [[0] * self.n for _ in range(self.n)]
        self._solve(board, 0)
        return self.solutions
    
    def _solve(self, board: List[List[int]], row: int):
        """Backtracking solver."""
        if row == self.n:
            # Found a solution
            self.solutions.append([r[:] for r in board])
            return
        
        for col in range(self.n):
            if self._is_safe(board, row, col):
                board[row][col] = 1
                self._solve(board, row + 1)
                board[row][col] = 0  # Backtrack
    
    def _is_safe(
        self,
        board: List[List[int]],
        row: int,
        col: int
    ) -> bool:
        """Check if position is safe for queen."""
        # Check column above
        for i in range(row):
            if board[i][col] == 1:
                return False
        
        # Check upper-left diagonal
        i, j = row - 1, col - 1
        while i >= 0 and j >= 0:
            if board[i][j] == 1:
                return False
            i -= 1
            j -= 1
        
        # Check upper-right diagonal
        i, j = row - 1, col + 1
        while i >= 0 and j < self.n:
            if board[i][j] == 1:
                return False
            i -= 1
            j += 1
        
        return True
    
    def print_solutions(self):
        """Print all solutions."""
        for idx, solution in enumerate(self.solutions):
            print(f"\nSolution {idx + 1}:")
            for row in solution:
                print(' '.join('Q' if cell else '.' for cell in row))


class NQueensOptimized:
    """
    Optimized N-Queens using sets for O(1) conflict checking.
    """
    
    def __init__(self, n: int):
        self.n = n
        self.solutions: List[List[int]] = []
    
    def solve(self) -> List[List[int]]:
        """
        Find all solutions.
        
        Returns list of solutions, each as column positions per row.
        
        >>> nq = NQueensOptimized(4)
        >>> solutions = nq.solve()
        >>> len(solutions)
        2
        >>> solutions[0]
        [1, 3, 0, 2]
        """
        self.solutions = []
        self._solve(
            queens=[],
            cols=set(),
            diag1=set(),
            diag2=set()
        )
        return self.solutions
    
    def _solve(
        self,
        queens: List[int],
        cols: Set[int],
        diag1: Set[int],
        diag2: Set[int]
    ):
        """Optimized backtracking with sets."""
        row = len(queens)
        
        if row == self.n:
            self.solutions.append(queens[:])
            return
        
        for col in range(self.n):
            d1 = row - col + self.n - 1
            d2 = row + col
            
            if col not in cols and d1 not in diag1 and d2 not in diag2:
                # Place queen
                queens.append(col)
                cols.add(col)
                diag1.add(d1)
                diag2.add(d2)
                
                self._solve(queens, cols, diag1, diag2)
                
                # Backtrack
                queens.pop()
                cols.remove(col)
                diag1.remove(d1)
                diag2.remove(d2)
    
    def count_solutions(self) -> int:
        """Count solutions without storing them."""
        return self._count(0, set(), set(), set())
    
    def _count(
        self,
        row: int,
        cols: Set[int],
        diag1: Set[int],
        diag2: Set[int]
    ) -> int:
        """Count solutions recursively."""
        if row == self.n:
            return 1
        
        count = 0
        for col in range(self.n):
            d1 = row - col + self.n - 1
            d2 = row + col
            
            if col not in cols and d1 not in diag1 and d2 not in diag2:
                cols.add(col)
                diag1.add(d1)
                diag2.add(d2)
                
                count += self._count(row + 1, cols, diag1, diag2)
                
                cols.remove(col)
                diag1.remove(d1)
                diag2.remove(d2)
        
        return count


class NQueensBitmask:
    """
    Highly optimized N-Queens using bitmasks.
    
    Uses bit manipulation for O(1) conflict detection.
    """
    
    def __init__(self, n: int):
        self.n = n
        self.all_ones = (1 << n) - 1
    
    def count_solutions(self) -> int:
        """
        Count all solutions using bitmask optimization.
        
        >>> nq = NQueensBitmask(8)
        >>> nq.count_solutions()
        92
        """
        return self._solve(0, 0, 0)
    
    def _solve(
        self,
        cols: int,
        diag1: int,
        diag2: int
    ) -> int:
        """Bitmask backtracking."""
        if cols == self.all_ones:
            return 1
        
        # Available positions
        available = self.all_ones & ~(cols | diag1 | diag2)
        
        count = 0
        while available:
            # Get rightmost set bit
            pos = available & (-available)
            available -= pos
            
            # Recurse with updated masks
            count += self._solve(
                cols | pos,
                (diag1 | pos) << 1,
                (diag2 | pos) >> 1
            )
        
        return count
    
    def solve(self) -> List[List[int]]:
        """Find all solutions with bitmask."""
        solutions = []
        self._solve_all(0, 0, 0, [], solutions)
        return solutions
    
    def _solve_all(
        self,
        cols: int,
        diag1: int,
        diag2: int,
        current: List[int],
        solutions: List[List[int]]
    ):
        """Find all solutions."""
        row = len(current)
        
        if row == self.n:
            solutions.append(current[:])
            return
        
        available = self.all_ones & ~(cols | diag1 | diag2)
        
        col = 0
        while available:
            if available & 1:
                current.append(col)
                self._solve_all(
                    cols | (1 << col),
                    (diag1 | (1 << col)) << 1,
                    (diag2 | (1 << col)) >> 1,
                    current,
                    solutions
                )
                current.pop()
            
            available >>= 1
            col += 1


def n_queens_all_solutions(n: int) -> List[List[str]]:
    """
    Solve N-Queens and return board representations.
    
    >>> solutions = n_queens_all_solutions(4)
    >>> len(solutions)
    2
    >>> solutions[0]
    ['.Q..', '...Q', 'Q...', '..Q.']
    """
    def solve(row, cols, d1, d2, queens):
        if row == n:
            board = []
            for r in range(n):
                row_str = '.' * queens[r] + 'Q' + '.' * (n - queens[r] - 1)
                board.append(row_str)
            results.append(board)
            return
        
        for col in range(n):
            if col not in cols and (row - col) not in d1 and (row + col) not in d2:
                cols.add(col)
                d1.add(row - col)
                d2.add(row + col)
                queens.append(col)
                
                solve(row + 1, cols, d1, d2, queens)
                
                queens.pop()
                cols.remove(col)
                d1.remove(row - col)
                d2.remove(row + col)
    
    results = []
    solve(0, set(), set(), set(), [])
    return results
```

## 7. Applications

### 7.1 Direct Applications

- Chess puzzles and variants
- Parallel memory storage
- VLSI circuit testing
- Constraint satisfaction problems

### 7.2 Related Problems

| Problem | Modification |
|---------|--------------|
| N-Rooks | Only row/column constraints |
| Super Queens | Also knight moves |
| Domination | Minimum queens to cover board |
| Independent sets | Graph theory generalization |

## 8. Real-World Software Engineering Applications

### 8.1 Production Example: Resource Allocation System

```python
from typing import List, Dict, Set, Tuple, Optional
from dataclasses import dataclass
from datetime import datetime, timedelta


@dataclass
class Resource:
    """A resource that can be allocated."""
    id: str
    name: str
    category: str
    capacity: int


@dataclass
class TimeSlot:
    """A time slot for allocation."""
    id: str
    start: datetime
    end: datetime


@dataclass
class Constraint:
    """Allocation constraint."""
    type: str  # 'exclusive', 'required', 'forbidden'
    resources: List[str]


class ResourceAllocator:
    """
    Resource allocation using N-Queens-style backtracking.
    
    Allocates resources to time slots with conflict avoidance.
    """
    
    def __init__(self):
        self.resources: Dict[str, Resource] = {}
        self.time_slots: List[TimeSlot] = []
        self.constraints: List[Constraint] = []
        
        # Conflict tracking (like queens)
        self.resource_conflicts: Dict[str, Set[str]] = {}
        self.category_limits: Dict[str, int] = {}
    
    def add_resource(self, resource: Resource):
        """Add a resource."""
        self.resources[resource.id] = resource
        self.resource_conflicts[resource.id] = set()
    
    def add_conflict(self, res1: str, res2: str):
        """Mark two resources as mutually exclusive."""
        self.resource_conflicts[res1].add(res2)
        self.resource_conflicts[res2].add(res1)
    
    def add_time_slots(self, slots: List[TimeSlot]):
        """Add time slots."""
        self.time_slots = slots
    
    def allocate(
        self,
        requests: List[Dict]
    ) -> Optional[Dict[str, List[str]]]:
        """
        Allocate resources to time slots.
        
        Args:
            requests: List of {'slot_id', 'resource_category', 'count'}
        
        Returns:
            Mapping of slot_id to list of resource_ids, or None if impossible.
        """
        # Initialize allocation
        allocation: Dict[str, List[str]] = {
            slot.id: [] for slot in self.time_slots
        }
        
        # Track used resources per slot
        used_per_slot: Dict[str, Set[str]] = {
            slot.id: set() for slot in self.time_slots
        }
        
        # Sort requests by constraint level (most constrained first)
        sorted_requests = sorted(
            requests,
            key=lambda r: self._constraint_score(r),
            reverse=True
        )
        
        if self._solve(sorted_requests, 0, allocation, used_per_slot):
            return allocation
        return None
    
    def _constraint_score(self, request: Dict) -> int:
        """Calculate how constrained a request is."""
        category = request['resource_category']
        available = sum(
            1 for r in self.resources.values()
            if r.category == category
        )
        return request['count'] * 100 - available
    
    def _solve(
        self,
        requests: List[Dict],
        idx: int,
        allocation: Dict[str, List[str]],
        used: Dict[str, Set[str]]
    ) -> bool:
        """Backtracking solver."""
        if idx >= len(requests):
            return True
        
        request = requests[idx]
        slot_id = request['slot_id']
        category = request['resource_category']
        count = request['count']
        
        # Get available resources of required category
        available = [
            r for r in self.resources.values()
            if r.category == category and r.id not in used[slot_id]
        ]
        
        # Try to allocate 'count' resources
        return self._try_allocate(
            available, count, slot_id, allocation, used,
            requests, idx
        )
    
    def _try_allocate(
        self,
        available: List[Resource],
        count: int,
        slot_id: str,
        allocation: Dict[str, List[str]],
        used: Dict[str, Set[str]],
        requests: List[Dict],
        idx: int
    ) -> bool:
        """Try to allocate resources recursively."""
        if count == 0:
            # Successfully allocated required count
            return self._solve(requests, idx + 1, allocation, used)
        
        for i, resource in enumerate(available):
            # Check conflicts (like queen attacks)
            if self._has_conflict(resource.id, slot_id, used):
                continue
            
            # Allocate
            allocation[slot_id].append(resource.id)
            used[slot_id].add(resource.id)
            
            # Recurse with remaining resources
            remaining = available[i + 1:]
            if self._try_allocate(
                remaining, count - 1, slot_id,
                allocation, used, requests, idx
            ):
                return True
            
            # Backtrack
            allocation[slot_id].pop()
            used[slot_id].remove(resource.id)
        
        return False
    
    def _has_conflict(
        self,
        resource_id: str,
        slot_id: str,
        used: Dict[str, Set[str]]
    ) -> bool:
        """Check if resource conflicts with already allocated."""
        conflicts = self.resource_conflicts.get(resource_id, set())
        return bool(conflicts & used[slot_id])
    
    def find_all_allocations(
        self,
        requests: List[Dict],
        max_solutions: int = 10
    ) -> List[Dict[str, List[str]]]:
        """Find all valid allocations."""
        solutions = []
        allocation = {slot.id: [] for slot in self.time_slots}
        used = {slot.id: set() for slot in self.time_slots}
        
        self._find_all(requests, 0, allocation, used, solutions, max_solutions)
        
        return solutions
    
    def _find_all(
        self,
        requests: List[Dict],
        idx: int,
        allocation: Dict[str, List[str]],
        used: Dict[str, Set[str]],
        solutions: List[Dict],
        max_solutions: int
    ):
        """Find all solutions."""
        if len(solutions) >= max_solutions:
            return
        
        if idx >= len(requests):
            solutions.append({
                k: v[:] for k, v in allocation.items()
            })
            return
        
        # Similar logic to _solve but continues after finding solutions
        request = requests[idx]
        slot_id = request['slot_id']
        category = request['resource_category']
        
        available = [
            r for r in self.resources.values()
            if r.category == category and r.id not in used[slot_id]
        ]
        
        self._try_all_allocations(
            available, request['count'], slot_id,
            allocation, used, requests, idx, solutions, max_solutions
        )
    
    def _try_all_allocations(
        self,
        available: List[Resource],
        count: int,
        slot_id: str,
        allocation: Dict[str, List[str]],
        used: Dict[str, Set[str]],
        requests: List[Dict],
        idx: int,
        solutions: List[Dict],
        max_solutions: int
    ):
        """Try all allocation combinations."""
        if len(solutions) >= max_solutions:
            return
        
        if count == 0:
            self._find_all(
                requests, idx + 1, allocation, used,
                solutions, max_solutions
            )
            return
        
        for i, resource in enumerate(available):
            if self._has_conflict(resource.id, slot_id, used):
                continue
            
            allocation[slot_id].append(resource.id)
            used[slot_id].add(resource.id)
            
            self._try_all_allocations(
                available[i + 1:], count - 1, slot_id,
                allocation, used, requests, idx,
                solutions, max_solutions
            )
            
            allocation[slot_id].pop()
            used[slot_id].remove(resource.id)


# Demo
if __name__ == "__main__":
    print("N-Queens Demo")
    print("=" * 50)
    
    # Basic N-Queens
    print("\n1. N-Queens Solutions:")
    
    for n in [4, 8]:
        nq = NQueensOptimized(n)
        solutions = nq.solve()
        print(f"   {n}-Queens: {len(solutions)} solutions")
        
        if n == 4:
            print(f"   First solution: {solutions[0]}")
    
    # Bitmask version
    print("\n2. Bitmask Optimization (counting only):")
    
    for n in [8, 10, 12]:
        nq = NQueensBitmask(n)
        count = nq.count_solutions()
        print(f"   {n}-Queens: {count} solutions")
    
    # Print a solution
    print("\n3. 8-Queens Solution:")
    solutions = n_queens_all_solutions(8)
    for row in solutions[0]:
        print(f"   {row}")
    
    # Resource allocation example
    print("\n4. Resource Allocation Example:")
    allocator = ResourceAllocator()
    
    # Add resources
    for i in range(5):
        allocator.add_resource(Resource(
            id=f"room_{i}",
            name=f"Meeting Room {i+1}",
            category="room",
            capacity=10
        ))
    
    # Add conflicts (some rooms share walls)
    allocator.add_conflict("room_0", "room_1")
    allocator.add_conflict("room_2", "room_3")
    
    # Add time slots
    allocator.add_time_slots([
        TimeSlot("slot_1", datetime.now(), datetime.now() + timedelta(hours=1)),
        TimeSlot("slot_2", datetime.now() + timedelta(hours=1), datetime.now() + timedelta(hours=2))
    ])
    
    # Try to allocate
    requests = [
        {'slot_id': 'slot_1', 'resource_category': 'room', 'count': 2},
        {'slot_id': 'slot_2', 'resource_category': 'room', 'count': 2}
    ]
    
    result = allocator.allocate(requests)
    if result:
        print("   Allocation successful!")
        for slot_id, resources in result.items():
            print(f"   {slot_id}: {resources}")
```

## 9. Variations

| Variation | Description |
|-----------|-------------|
| N-Rooks | No diagonal constraints |
| Super Queens | Queen + Knight moves |
| N-Queens Completion | Some queens pre-placed |
| Minimum Dominating Set | Fewest queens to cover all squares |
| N-Queens with obstacles | Some squares blocked |

## 10. Best Practices

1. **Use sets for O(1) lookups** instead of array scanning
2. **Process by row** to ensure one queen per row
3. **Prune early** with constraint propagation
4. **Use bitmasks** for maximum performance
5. **Exploit symmetry** to reduce search space
6. **Order choices** by most constrained first

## 11. References

- [Wikipedia: N-Queens](https://en.wikipedia.org/wiki/Eight_queens_puzzle)
- Wirth, N. "Algorithms + Data Structures = Programs" (1976)
- Russell, S. & Norvig, P. "Artificial Intelligence: A Modern Approach"
