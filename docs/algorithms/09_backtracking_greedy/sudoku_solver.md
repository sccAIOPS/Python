# Sudoku Solver

## Overview
- **Category**: Backtracking / Constraint Satisfaction
- **Complexity**: Time: O(9^(n²)) worst | Space: O(n²)
- **Type**: Constraint propagation + Search
- **Source Files**: [backtracking/sudoku.py](../../../backtracking/sudoku.py)

## 1. Mathematical Foundation

### 1.1 Problem Definition

Fill a 9×9 grid with digits 1-9 such that:
- Each row contains digits 1-9 exactly once
- Each column contains digits 1-9 exactly once
- Each 3×3 sub-box contains digits 1-9 exactly once

### 1.2 Constraint Formulation

For cell $(r, c)$ with value $v$:

**Row constraint**: $\forall c' \neq c: \text{grid}[r][c'] \neq v$

**Column constraint**: $\forall r' \neq r: \text{grid}[r'][c] \neq v$

**Box constraint**: For all cells in the same 3×3 box, value is unique

**Box index**:
$$
\text{box\_row} = \lfloor r / 3 \rfloor \times 3
$$
$$
\text{box\_col} = \lfloor c / 3 \rfloor \times 3
$$

### 1.3 Solution Space

- **Empty cells**: Let $k$ = number of empty cells
- **Naive search space**: $9^k$ (each cell has 9 choices)
- **With constraints**: Much smaller due to pruning

### 1.4 Uniqueness

A well-formed Sudoku puzzle has exactly one solution.

**Minimum clues**: 17 clues are necessary for unique solution.

### 1.5 Latin Square Relationship

Sudoku is a special case of a Latin square with additional box constraints.

**Latin square**: n×n array filled with n different symbols, each occurring exactly once in each row and column.

## 2. Pseudocode

### 2.1 Basic Backtracking

```
ALGORITHM SolveSudoku(grid)
    INPUT: 9×9 grid (0 represents empty)
    OUTPUT: True if solvable, False otherwise
    
    // Find empty cell
    cell ← FindEmptyCell(grid)
    
    if cell is None then
        return True  // Puzzle solved
    
    row, col ← cell
    
    for num ← 1 to 9 do
        if IsValid(grid, row, col, num) then
            grid[row][col] ← num
            
            if SolveSudoku(grid) then
                return True
            
            grid[row][col] ← 0  // Backtrack
    
    return False

ALGORITHM IsValid(grid, row, col, num)
    INPUT: Grid, position, number to place
    OUTPUT: True if placement is valid
    
    // Check row
    for c ← 0 to 8 do
        if grid[row][c] = num then
            return False
    
    // Check column
    for r ← 0 to 8 do
        if grid[r][col] = num then
            return False
    
    // Check 3×3 box
    box_row ← (row / 3) × 3
    box_col ← (col / 3) × 3
    
    for r ← box_row to box_row + 2 do
        for c ← box_col to box_col + 2 do
            if grid[r][c] = num then
                return False
    
    return True
```

### 2.2 Optimized with Constraint Propagation

```
ALGORITHM SolveSudokuOptimized(grid)
    INPUT: 9×9 grid
    OUTPUT: Solved grid or None
    
    // Initialize possible values for each cell
    possible ← InitializePossibilities(grid)
    
    // Propagate constraints
    if not Propagate(grid, possible) then
        return None
    
    // Find unsolved cell with minimum possibilities (MRV heuristic)
    cell ← SelectUnassignedVariable(grid, possible)
    
    if cell is None then
        return grid  // Solved
    
    row, col ← cell
    
    for num in possible[row][col] do
        // Make a copy
        grid_copy ← copy(grid)
        possible_copy ← copy(possible)
        
        grid_copy[row][col] ← num
        
        result ← SolveSudokuOptimized(grid_copy)
        if result is not None then
            return result
    
    return None

ALGORITHM Propagate(grid, possible)
    INPUT: Grid and possibilities
    OUTPUT: True if consistent, False if contradiction
    
    changed ← True
    
    while changed do
        changed ← False
        
        // Naked singles: cell with only one possibility
        for each cell (r, c) do
            if grid[r][c] = 0 AND |possible[r][c]| = 1 then
                num ← only element in possible[r][c]
                grid[r][c] ← num
                UpdatePossibilities(possible, r, c, num)
                changed ← True
            
            if |possible[r][c]| = 0 AND grid[r][c] = 0 then
                return False  // Contradiction
        
        // Hidden singles: value possible in only one place in unit
        for each unit (row, column, box) do
            for num ← 1 to 9 do
                places ← cells in unit where num is possible
                if |places| = 1 then
                    r, c ← places[0]
                    if grid[r][c] = 0 then
                        grid[r][c] ← num
                        UpdatePossibilities(possible, r, c, num)
                        changed ← True
                elif |places| = 0 then
                    return False  // Contradiction
    
    return True
```

### 2.3 Dancing Links (DLX) Approach

```
ALGORITHM SudokuAsDLX(grid)
    // Convert Sudoku to exact cover problem
    // 324 columns: 81 cell + 81 row + 81 col + 81 box constraints
    // Up to 729 rows: each possible placement
    
    matrix ← BuildExactCoverMatrix(grid)
    
    // Solve using Algorithm X with Dancing Links
    solution ← AlgorithmX(matrix)
    
    return ConvertToGrid(solution)
```

## 3. Step-by-Step Example

### Example Puzzle

```
Initial:
5 3 . │ . 7 . │ . . .
6 . . │ 1 9 5 │ . . .
. 9 8 │ . . . │ . 6 .
──────┼───────┼──────
8 . . │ . 6 . │ . . 3
4 . . │ 8 . 3 │ . . 1
7 . . │ . 2 . │ . . 6
──────┼───────┼──────
. 6 . │ . . . │ 2 8 .
. . . │ 4 1 9 │ . . 5
. . . │ . 8 . │ . 7 9
```

### Step 1: Find Empty Cell (0,2)
- Row 0 has: 5, 3, 7
- Column 2 has: 8
- Box 0 has: 5, 3, 6, 9, 8
- Possible: {1, 2, 4}

### Step 2: Try 1 at (0,2)
- Check constraints...
- Valid! Place 1

### Step 3: Continue to (0,3)
- Possible: {2, 4, 6}
- Try 2...

### ... Backtracking occurs when dead end reached

### Final Solution:
```
5 3 4 │ 6 7 8 │ 9 1 2
6 7 2 │ 1 9 5 │ 3 4 8
1 9 8 │ 3 4 2 │ 5 6 7
──────┼───────┼──────
8 5 9 │ 7 6 1 │ 4 2 3
4 2 6 │ 8 5 3 │ 7 9 1
7 1 3 │ 9 2 4 │ 8 5 6
──────┼───────┼──────
9 6 1 │ 5 3 7 │ 2 8 4
2 8 7 │ 4 1 9 │ 6 3 5
3 4 5 │ 2 8 6 │ 1 7 9
```

## 4. Complexity Analysis

| Approach | Time Complexity | Space |
|----------|----------------|-------|
| Brute force | O(9^81) | O(1) |
| Basic backtracking | O(9^k), k=empty | O(k) |
| With constraint propagation | Much better avg | O(81) |
| Dancing Links | O(branches) | O(columns) |

**Practical performance**: Most puzzles solve in milliseconds.

## 5. Visual Representation

### 5.1 Constraint Graph

```
Each cell has 20 neighbors (constraints):
- 8 in same row
- 8 in same column  
- 4 in same box (not row/col)

Cell (4,4) constraints:

    0 1 2 3 4 5 6 7 8
   ┌─────────────────┐
 0 │    │    R    │  │
 1 │    │    R    │  │
 2 │    │    R    │  │
   │────┼────┼────┼──│
 3 │    │B B R    │  │
 4 │C C C│B X R   │  │  X = cell (4,4)
 5 │    │B B R    │  │  R = row constraint
   │────┼────┼────┼──│  C = column constraint
 6 │    │    R    │  │  B = box constraint
 7 │    │    R    │  │
 8 │    │    R    │  │
   └─────────────────┘
```

### 5.2 Search Tree

```
                        Root
                      /  |  \
                   1    2    4     (cell 0,2 possibilities)
                  /|\  /|\  /|\
                 ...  ...  ...     (cell 0,3 possibilities)
                  |
                 ...
                  |
              Solution or Backtrack
```

### 5.3 Constraint Propagation

```
Initial possibilities for cell (0,2):
{1, 2, 3, 4, 5, 6, 7, 8, 9}

After row constraint (5,3,7 exist):
{1, 2, 4, 6, 8, 9}

After column constraint (8 exists):
{1, 2, 4, 6, 9}

After box constraint (5,3,6,9,8 exist):
{1, 2, 4}
```

## 6. Implementation

```python
from typing import List, Set, Tuple, Optional, Dict
from copy import deepcopy


class SudokuSolver:
    """
    Sudoku solver using backtracking with constraint propagation.
    """
    
    def __init__(self):
        self.grid: List[List[int]] = []
        self.possible: List[List[Set[int]]] = []
        self.iterations: int = 0
    
    def solve(self, grid: List[List[int]]) -> Optional[List[List[int]]]:
        """
        Solve Sudoku puzzle.
        
        >>> solver = SudokuSolver()
        >>> grid = [
        ...     [5,3,0,0,7,0,0,0,0],
        ...     [6,0,0,1,9,5,0,0,0],
        ...     [0,9,8,0,0,0,0,6,0],
        ...     [8,0,0,0,6,0,0,0,3],
        ...     [4,0,0,8,0,3,0,0,1],
        ...     [7,0,0,0,2,0,0,0,6],
        ...     [0,6,0,0,0,0,2,8,0],
        ...     [0,0,0,4,1,9,0,0,5],
        ...     [0,0,0,0,8,0,0,7,9]
        ... ]
        >>> result = solver.solve(grid)
        >>> result[0][0]
        5
        >>> result[0][2]
        4
        """
        self.grid = [row[:] for row in grid]
        self.iterations = 0
        
        # Initialize possibilities
        self._init_possible()
        
        # Initial constraint propagation
        if not self._propagate():
            return None
        
        # Solve with backtracking
        if self._solve():
            return self.grid
        return None
    
    def _init_possible(self):
        """Initialize possible values for each cell."""
        self.possible = [
            [set(range(1, 10)) for _ in range(9)]
            for _ in range(9)
        ]
        
        for r in range(9):
            for c in range(9):
                if self.grid[r][c] != 0:
                    self.possible[r][c] = set()
                    self._eliminate(r, c, self.grid[r][c])
    
    def _eliminate(self, row: int, col: int, num: int):
        """Eliminate num from all peers of (row, col)."""
        # Row
        for c in range(9):
            self.possible[row][c].discard(num)
        
        # Column
        for r in range(9):
            self.possible[r][col].discard(num)
        
        # Box
        box_r, box_c = (row // 3) * 3, (col // 3) * 3
        for r in range(box_r, box_r + 3):
            for c in range(box_c, box_c + 3):
                self.possible[r][c].discard(num)
    
    def _propagate(self) -> bool:
        """
        Apply constraint propagation techniques.
        Returns False if contradiction found.
        """
        changed = True
        
        while changed:
            changed = False
            
            # Naked singles
            for r in range(9):
                for c in range(9):
                    if self.grid[r][c] == 0:
                        if len(self.possible[r][c]) == 0:
                            return False  # Contradiction
                        
                        if len(self.possible[r][c]) == 1:
                            num = next(iter(self.possible[r][c]))
                            self.grid[r][c] = num
                            self._eliminate(r, c, num)
                            self.possible[r][c] = set()
                            changed = True
            
            # Hidden singles in rows
            for r in range(9):
                for num in range(1, 10):
                    if any(self.grid[r][c] == num for c in range(9)):
                        continue
                    
                    positions = [
                        c for c in range(9)
                        if self.grid[r][c] == 0 and num in self.possible[r][c]
                    ]
                    
                    if len(positions) == 0:
                        return False
                    if len(positions) == 1:
                        c = positions[0]
                        self.grid[r][c] = num
                        self._eliminate(r, c, num)
                        self.possible[r][c] = set()
                        changed = True
            
            # Hidden singles in columns
            for c in range(9):
                for num in range(1, 10):
                    if any(self.grid[r][c] == num for r in range(9)):
                        continue
                    
                    positions = [
                        r for r in range(9)
                        if self.grid[r][c] == 0 and num in self.possible[r][c]
                    ]
                    
                    if len(positions) == 0:
                        return False
                    if len(positions) == 1:
                        r = positions[0]
                        self.grid[r][c] = num
                        self._eliminate(r, c, num)
                        self.possible[r][c] = set()
                        changed = True
            
            # Hidden singles in boxes
            for box_r in range(0, 9, 3):
                for box_c in range(0, 9, 3):
                    for num in range(1, 10):
                        found = False
                        for r in range(box_r, box_r + 3):
                            for c in range(box_c, box_c + 3):
                                if self.grid[r][c] == num:
                                    found = True
                                    break
                            if found:
                                break
                        
                        if found:
                            continue
                        
                        positions = []
                        for r in range(box_r, box_r + 3):
                            for c in range(box_c, box_c + 3):
                                if self.grid[r][c] == 0 and num in self.possible[r][c]:
                                    positions.append((r, c))
                        
                        if len(positions) == 0:
                            return False
                        if len(positions) == 1:
                            r, c = positions[0]
                            self.grid[r][c] = num
                            self._eliminate(r, c, num)
                            self.possible[r][c] = set()
                            changed = True
        
        return True
    
    def _solve(self) -> bool:
        """Backtracking search with MRV heuristic."""
        self.iterations += 1
        
        # Find cell with minimum remaining values (MRV)
        min_count = 10
        best_cell = None
        
        for r in range(9):
            for c in range(9):
                if self.grid[r][c] == 0:
                    count = len(self.possible[r][c])
                    if count == 0:
                        return False
                    if count < min_count:
                        min_count = count
                        best_cell = (r, c)
        
        if best_cell is None:
            return True  # Solved
        
        row, col = best_cell
        
        for num in list(self.possible[row][col]):
            # Save state
            old_grid = [r[:] for r in self.grid]
            old_possible = [[c.copy() for c in r] for r in self.possible]
            
            # Make move
            self.grid[row][col] = num
            self._eliminate(row, col, num)
            self.possible[row][col] = set()
            
            # Propagate and solve
            if self._propagate() and self._solve():
                return True
            
            # Restore state (backtrack)
            self.grid = old_grid
            self.possible = old_possible
        
        return False
    
    def print_grid(self):
        """Print the current grid."""
        for i, row in enumerate(self.grid):
            if i % 3 == 0 and i != 0:
                print("------+-------+------")
            
            line = ""
            for j, cell in enumerate(row):
                if j % 3 == 0 and j != 0:
                    line += "| "
                line += str(cell) if cell != 0 else "."
                line += " "
            print(line)


class SudokuSolverSimple:
    """
    Simple backtracking Sudoku solver.
    """
    
    def solve(self, grid: List[List[int]]) -> bool:
        """
        Solve puzzle in place.
        
        >>> solver = SudokuSolverSimple()
        >>> grid = [[0]*9 for _ in range(9)]
        >>> grid[0][0] = 5
        >>> # solver.solve(grid)  # Would solve
        """
        empty = self._find_empty(grid)
        
        if empty is None:
            return True
        
        row, col = empty
        
        for num in range(1, 10):
            if self._is_valid(grid, row, col, num):
                grid[row][col] = num
                
                if self.solve(grid):
                    return True
                
                grid[row][col] = 0
        
        return False
    
    def _find_empty(
        self,
        grid: List[List[int]]
    ) -> Optional[Tuple[int, int]]:
        """Find an empty cell."""
        for r in range(9):
            for c in range(9):
                if grid[r][c] == 0:
                    return (r, c)
        return None
    
    def _is_valid(
        self,
        grid: List[List[int]],
        row: int,
        col: int,
        num: int
    ) -> bool:
        """Check if placing num at (row, col) is valid."""
        # Check row
        if num in grid[row]:
            return False
        
        # Check column
        if num in (grid[r][col] for r in range(9)):
            return False
        
        # Check 3x3 box
        box_r, box_c = (row // 3) * 3, (col // 3) * 3
        for r in range(box_r, box_r + 3):
            for c in range(box_c, box_c + 3):
                if grid[r][c] == num:
                    return False
        
        return True


class SudokuGenerator:
    """
    Generate Sudoku puzzles with unique solutions.
    """
    
    def __init__(self):
        self.solver = SudokuSolver()
    
    def generate(
        self,
        difficulty: str = 'medium'
    ) -> List[List[int]]:
        """
        Generate a Sudoku puzzle.
        
        Args:
            difficulty: 'easy', 'medium', 'hard', 'expert'
        """
        import random
        
        # Start with solved grid
        grid = [[0] * 9 for _ in range(9)]
        self._fill_grid(grid)
        
        # Remove cells based on difficulty
        cells_to_remove = {
            'easy': 30,
            'medium': 40,
            'hard': 50,
            'expert': 56
        }.get(difficulty, 40)
        
        # Get all positions and shuffle
        positions = [(r, c) for r in range(9) for c in range(9)]
        random.shuffle(positions)
        
        removed = 0
        for r, c in positions:
            if removed >= cells_to_remove:
                break
            
            backup = grid[r][c]
            grid[r][c] = 0
            
            # Check if still unique solution
            if not self._has_unique_solution(grid):
                grid[r][c] = backup
            else:
                removed += 1
        
        return grid
    
    def _fill_grid(self, grid: List[List[int]]) -> bool:
        """Fill grid with valid solution."""
        import random
        
        empty = self._find_empty(grid)
        if empty is None:
            return True
        
        row, col = empty
        numbers = list(range(1, 10))
        random.shuffle(numbers)
        
        for num in numbers:
            if self._is_valid(grid, row, col, num):
                grid[row][col] = num
                if self._fill_grid(grid):
                    return True
                grid[row][col] = 0
        
        return False
    
    def _find_empty(self, grid):
        for r in range(9):
            for c in range(9):
                if grid[r][c] == 0:
                    return (r, c)
        return None
    
    def _is_valid(self, grid, row, col, num):
        # Check row
        if num in grid[row]:
            return False
        # Check column
        if num in (grid[r][col] for r in range(9)):
            return False
        # Check box
        box_r, box_c = (row // 3) * 3, (col // 3) * 3
        for r in range(box_r, box_r + 3):
            for c in range(box_c, box_c + 3):
                if grid[r][c] == num:
                    return False
        return True
    
    def _has_unique_solution(self, grid: List[List[int]]) -> bool:
        """Check if puzzle has exactly one solution."""
        solutions = [0]
        grid_copy = [row[:] for row in grid]
        self._count_solutions(grid_copy, solutions, 2)
        return solutions[0] == 1
    
    def _count_solutions(
        self,
        grid: List[List[int]],
        count: List[int],
        limit: int
    ) -> bool:
        """Count solutions up to limit."""
        if count[0] >= limit:
            return True
        
        empty = self._find_empty(grid)
        if empty is None:
            count[0] += 1
            return count[0] >= limit
        
        row, col = empty
        
        for num in range(1, 10):
            if self._is_valid(grid, row, col, num):
                grid[row][col] = num
                if self._count_solutions(grid, count, limit):
                    grid[row][col] = 0
                    return True
                grid[row][col] = 0
        
        return False


def is_valid_sudoku(grid: List[List[int]]) -> bool:
    """
    Check if a completed Sudoku grid is valid.
    
    >>> grid = [[5,3,4,6,7,8,9,1,2],
    ...         [6,7,2,1,9,5,3,4,8],
    ...         [1,9,8,3,4,2,5,6,7],
    ...         [8,5,9,7,6,1,4,2,3],
    ...         [4,2,6,8,5,3,7,9,1],
    ...         [7,1,3,9,2,4,8,5,6],
    ...         [9,6,1,5,3,7,2,8,4],
    ...         [2,8,7,4,1,9,6,3,5],
    ...         [3,4,5,2,8,6,1,7,9]]
    >>> is_valid_sudoku(grid)
    True
    """
    # Check rows
    for row in grid:
        if len(set(row)) != 9 or not all(1 <= x <= 9 for x in row):
            return False
    
    # Check columns
    for c in range(9):
        col = [grid[r][c] for r in range(9)]
        if len(set(col)) != 9:
            return False
    
    # Check boxes
    for box_r in range(0, 9, 3):
        for box_c in range(0, 9, 3):
            box = []
            for r in range(box_r, box_r + 3):
                for c in range(box_c, box_c + 3):
                    box.append(grid[r][c])
            if len(set(box)) != 9:
                return False
    
    return True
```

## 7. Applications

### 7.1 Direct Applications

- Puzzle games and apps
- Constraint satisfaction research
- Educational tools
- Logic training

### 7.2 Related Problems

| Problem | Relationship |
|---------|--------------|
| Latin squares | Generalization |
| Graph coloring | Same constraint type |
| Exact cover | Can be reduced to |
| SAT | Boolean satisfiability |

## 8. Real-World Software Engineering Applications

### 8.1 Production Example: Scheduling Constraint Solver

```python
from typing import List, Dict, Set, Tuple, Optional
from dataclasses import dataclass
from datetime import datetime, time
from enum import Enum


class ConstraintType(Enum):
    UNIQUE_ROW = "unique_row"
    UNIQUE_COL = "unique_col"
    UNIQUE_BOX = "unique_box"
    CUSTOM = "custom"


@dataclass
class ScheduleSlot:
    """A slot in the schedule grid."""
    row_id: str  # e.g., employee
    col_id: str  # e.g., time slot
    value: Optional[str] = None  # e.g., task assignment


class ScheduleConstraintSolver:
    """
    Sudoku-style constraint solver for scheduling problems.
    
    Maps scheduling to a constraint satisfaction problem similar to Sudoku.
    """
    
    def __init__(
        self,
        rows: List[str],
        columns: List[str],
        values: List[str]
    ):
        """
        Initialize scheduler.
        
        Args:
            rows: Row identifiers (e.g., employees)
            columns: Column identifiers (e.g., time slots)
            values: Possible values (e.g., tasks)
        """
        self.rows = rows
        self.columns = columns
        self.values = values
        
        self.n_rows = len(rows)
        self.n_cols = len(columns)
        self.n_values = len(values)
        
        # Grid: rows × columns → value
        self.grid: List[List[Optional[str]]] = [
            [None] * self.n_cols for _ in range(self.n_rows)
        ]
        
        # Possible values per cell
        self.possible: List[List[Set[str]]] = [
            [set(values) for _ in range(self.n_cols)]
            for _ in range(self.n_rows)
        ]
        
        # Custom constraints
        self.row_constraints: Dict[str, List[str]] = {}
        self.col_constraints: Dict[str, List[str]] = {}
        self.incompatible: Dict[str, Set[str]] = {v: set() for v in values}
        
        # Pre-assignments
        self.fixed: Set[Tuple[int, int]] = set()
    
    def add_row_constraint(self, row_id: str, allowed_values: List[str]):
        """Restrict values allowed in a row."""
        self.row_constraints[row_id] = allowed_values
        
        if row_id in self.rows:
            r = self.rows.index(row_id)
            for c in range(self.n_cols):
                self.possible[r][c] &= set(allowed_values)
    
    def add_incompatibility(self, value1: str, value2: str):
        """Mark two values as incompatible in same row/column."""
        self.incompatible[value1].add(value2)
        self.incompatible[value2].add(value1)
    
    def assign(self, row_id: str, col_id: str, value: str):
        """Pre-assign a value to a cell."""
        r = self.rows.index(row_id)
        c = self.columns.index(col_id)
        
        self.grid[r][c] = value
        self.possible[r][c] = set()
        self.fixed.add((r, c))
        
        # Propagate constraints
        self._eliminate(r, c, value)
    
    def _eliminate(self, row: int, col: int, value: str):
        """Eliminate value from peers."""
        # Same value can't appear twice in same row (if constraint enabled)
        for c in range(self.n_cols):
            if c != col:
                self.possible[row][c].discard(value)
        
        # Same value can't appear twice in same column
        for r in range(self.n_rows):
            if r != row:
                self.possible[r][col].discard(value)
        
        # Handle incompatible values
        incompatible = self.incompatible.get(value, set())
        for c in range(self.n_cols):
            for incompat in incompatible:
                self.possible[row][c].discard(incompat)
    
    def solve(self) -> Optional[List[List[Optional[str]]]]:
        """
        Solve the scheduling problem.
        
        Returns:
            Solved grid or None if no solution exists.
        """
        # Apply constraint propagation
        if not self._propagate():
            return None
        
        # Backtracking search
        if self._solve():
            return self.grid
        return None
    
    def _propagate(self) -> bool:
        """Apply constraint propagation."""
        changed = True
        
        while changed:
            changed = False
            
            for r in range(self.n_rows):
                for c in range(self.n_cols):
                    if self.grid[r][c] is not None:
                        continue
                    
                    if len(self.possible[r][c]) == 0:
                        return False  # Contradiction
                    
                    if len(self.possible[r][c]) == 1:
                        value = next(iter(self.possible[r][c]))
                        self.grid[r][c] = value
                        self._eliminate(r, c, value)
                        self.possible[r][c] = set()
                        changed = True
        
        return True
    
    def _solve(self) -> bool:
        """Backtracking with MRV."""
        # Find cell with minimum remaining values
        min_count = float('inf')
        best_cell = None
        
        for r in range(self.n_rows):
            for c in range(self.n_cols):
                if self.grid[r][c] is None:
                    count = len(self.possible[r][c])
                    if count == 0:
                        return False
                    if count < min_count:
                        min_count = count
                        best_cell = (r, c)
        
        if best_cell is None:
            return True  # Solved
        
        row, col = best_cell
        
        for value in list(self.possible[row][col]):
            # Save state
            old_grid = [r[:] for r in self.grid]
            old_possible = [[c.copy() for c in r] for r in self.possible]
            
            # Make assignment
            self.grid[row][col] = value
            self._eliminate(row, col, value)
            self.possible[row][col] = set()
            
            # Propagate and solve
            if self._propagate() and self._solve():
                return True
            
            # Restore state
            self.grid = old_grid
            self.possible = old_possible
        
        return False
    
    def get_schedule(self) -> Dict[str, Dict[str, Optional[str]]]:
        """Get schedule as dictionary."""
        schedule = {}
        
        for r, row_id in enumerate(self.rows):
            schedule[row_id] = {}
            for c, col_id in enumerate(self.columns):
                schedule[row_id][col_id] = self.grid[r][c]
        
        return schedule
    
    def print_schedule(self):
        """Print the schedule."""
        # Header
        header = "         | " + " | ".join(f"{c[:6]:^6}" for c in self.columns)
        print(header)
        print("-" * len(header))
        
        for r, row_id in enumerate(self.rows):
            row_str = f"{row_id[:8]:<8} | "
            row_str += " | ".join(
                f"{(self.grid[r][c] or '-')[:6]:^6}" 
                for c in range(self.n_cols)
            )
            print(row_str)


# Demo
if __name__ == "__main__":
    print("Sudoku Solver Demo")
    print("=" * 50)
    
    # Standard Sudoku
    print("\n1. Standard Sudoku:")
    
    puzzle = [
        [5,3,0,0,7,0,0,0,0],
        [6,0,0,1,9,5,0,0,0],
        [0,9,8,0,0,0,0,6,0],
        [8,0,0,0,6,0,0,0,3],
        [4,0,0,8,0,3,0,0,1],
        [7,0,0,0,2,0,0,0,6],
        [0,6,0,0,0,0,2,8,0],
        [0,0,0,4,1,9,0,0,5],
        [0,0,0,0,8,0,0,7,9]
    ]
    
    solver = SudokuSolver()
    solution = solver.solve(puzzle)
    
    if solution:
        print("   Solution found!")
        solver.print_grid()
        print(f"   Iterations: {solver.iterations}")
    
    # Scheduling example
    print("\n2. Scheduling Constraint Solver:")
    
    employees = ["Alice", "Bob", "Carol"]
    time_slots = ["Mon AM", "Mon PM", "Tue AM", "Tue PM"]
    tasks = ["Task A", "Task B", "Task C"]
    
    scheduler = ScheduleConstraintSolver(employees, time_slots, tasks)
    
    # Add constraints
    scheduler.add_row_constraint("Alice", ["Task A", "Task B"])  # Alice can't do Task C
    scheduler.add_incompatibility("Task A", "Task B")  # A and B can't be done together
    
    # Pre-assign
    scheduler.assign("Bob", "Mon AM", "Task A")
    
    result = scheduler.solve()
    if result:
        print("   Schedule found!")
        scheduler.print_schedule()
```

## 9. Variations

| Variation | Description |
|-----------|-------------|
| 16×16 Sudoku | Hex Sudoku with 4×4 boxes |
| 6×6 Mini | 2×3 boxes |
| Killer Sudoku | Sum cages |
| Samurai Sudoku | 5 overlapping grids |
| Jigsaw Sudoku | Irregular box shapes |

## 10. Best Practices

1. **Use constraint propagation** before searching
2. **Apply MRV heuristic** (most constrained variable first)
3. **Precompute peer lists** for O(1) lookup
4. **Use bitsets** for possible values (faster operations)
5. **Detect contradictions early** to prune search tree
6. **Cache intermediate results** when possible

## 11. References

- [Wikipedia: Sudoku](https://en.wikipedia.org/wiki/Sudoku)
- Norvig, P. "Solving Every Sudoku Puzzle" (2006)
- Knuth, D. "Dancing Links" (2000)
