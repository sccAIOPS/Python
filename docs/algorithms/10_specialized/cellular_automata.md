# Cellular Automata

## Overview
- **Category**: Computational Theory / Simulation
- **Complexity**: Time: O(n × t) | Space: O(n)
- **Type**: Discrete models, emergent behavior
- **Source Files**: [cellular_automata/](../../../cellular_automata/)

## 1. Mathematical Foundation

### 1.1 Definition

A cellular automaton consists of:
- **Grid**: $\mathcal{G} = \mathbb{Z}^d$ (d-dimensional lattice)
- **States**: $S = \{s_0, s_1, ..., s_{k-1}\}$
- **Neighborhood**: $N \subseteq \mathbb{Z}^d$
- **Transition Rule**: $f: S^{|N|} \to S$

### 1.2 1D Elementary Cellular Automata

For 1D with 2 states and 3-cell neighborhood:
$$
s_i^{t+1} = f(s_{i-1}^t, s_i^t, s_{i+1}^t)
$$

Number of possible rules: $2^{2^3} = 256$

### 1.3 Rule Numbering (Wolfram)

Rule number is binary representation of outputs:

| Pattern | 111 | 110 | 101 | 100 | 011 | 010 | 001 | 000 |
|---------|-----|-----|-----|-----|-----|-----|-----|-----|
| Rule 30 | 0 | 0 | 0 | 1 | 1 | 1 | 1 | 0 |
| Rule 110| 0 | 1 | 1 | 0 | 1 | 1 | 1 | 0 |

### 1.4 Conway's Game of Life

2D automaton with rules:
- **Birth**: Dead cell with exactly 3 neighbors becomes alive
- **Survival**: Live cell with 2-3 neighbors survives
- **Death**: Otherwise dies

$$
s_{i,j}^{t+1} = \begin{cases}
1 & \text{if } N(i,j) = 3 \\
s_{i,j}^t & \text{if } N(i,j) = 2 \\
0 & \text{otherwise}
\end{cases}
$$

Where $N(i,j) = \sum_{(x,y) \in \text{neighbors}} s_{x,y}^t$

### 1.5 Langton's λ Parameter

Measures rule complexity:
$$
\lambda = \frac{k^{|N|} - n_0}{k^{|N|}}
$$

Where $n_0$ = transitions to quiescent state.

- $\lambda \approx 0$: Ordered behavior
- $\lambda \approx 0.5$: Complex/chaotic edge
- $\lambda \approx 1$: Chaotic behavior

## 2. Automata Categories

### 2.1 1D Automata
- **Elementary**: 2 states, 3-cell neighborhood
- **Totalistic**: Based on sum of neighbors
- **Continuous**: Real-valued states

### 2.2 2D Automata
- **Game of Life**: Birth/death rules
- **Wireworld**: Circuit simulation
- **Langton's Ant**: Turmite-style

### 2.3 Classifications (Wolfram)

| Class | Behavior | Example |
|-------|----------|---------|
| I | Homogeneous | Rule 0 |
| II | Periodic | Rule 4 |
| III | Chaotic | Rule 30 |
| IV | Complex | Rule 110 |

## 3. Pseudocode

### 3.1 Elementary Cellular Automaton

```
ALGORITHM Elementary1D(initial, rule, steps)
    INPUT: Initial state, rule number, steps
    OUTPUT: State history
    
    current ← initial
    history ← [current]
    
    for t ← 1 to steps do
        next ← array of size |current|
        
        for i ← 0 to |current| - 1 do
            left ← current[i - 1 mod n]
            center ← current[i]
            right ← current[i + 1 mod n]
            
            pattern ← left × 4 + center × 2 + right
            next[i] ← (rule >> pattern) AND 1
        
        current ← next
        history.append(current)
    
    return history
```

### 3.2 Game of Life

```
ALGORITHM GameOfLife(grid, steps)
    INPUT: 2D grid, number of steps
    OUTPUT: Final grid state
    
    for t ← 1 to steps do
        next ← copy of grid
        
        for each cell (i, j) in grid do
            neighbors ← count_neighbors(grid, i, j)
            
            if grid[i][j] = 1 then  // Alive
                if neighbors < 2 or neighbors > 3 then
                    next[i][j] ← 0  // Dies
            else  // Dead
                if neighbors = 3 then
                    next[i][j] ← 1  // Born
        
        grid ← next
    
    return grid


ALGORITHM count_neighbors(grid, i, j)
    count ← 0
    for di ← -1 to 1 do
        for dj ← -1 to 1 do
            if (di, dj) ≠ (0, 0) then
                ni ← (i + di) mod rows
                nj ← (j + dj) mod cols
                count ← count + grid[ni][nj]
    return count
```

### 3.3 Langton's Ant

```
ALGORITHM LangtonsAnt(grid, steps)
    INPUT: Grid size, number of steps
    OUTPUT: Grid state, ant position
    
    position ← center of grid
    direction ← 0  // 0=up, 1=right, 2=down, 3=left
    
    for t ← 1 to steps do
        (x, y) ← position
        
        if grid[x][y] = 0 then  // White
            direction ← (direction + 1) mod 4  // Turn right
            grid[x][y] ← 1  // Flip to black
        else  // Black
            direction ← (direction - 1 + 4) mod 4  // Turn left
            grid[x][y] ← 0  // Flip to white
        
        // Move forward
        position ← move(position, direction)
    
    return grid, position
```

## 4. Step-by-Step Example

### Example 1: Rule 30

```
Rule 30 = 00011110 in binary

Pattern: 111 110 101 100 011 010 001 000
Output:   0   0   0   1   1   1   1   0

Initial: 00001000000 (single cell)

Step 1: 
  xxx0001000xxx
  ↓ Apply rule to each cell
  00011100000
  
Step 2:
  00011100000
  00110010000
  
Step 3:
  00110010000
  01101111000

Triangular pattern emerges!
```

### Example 2: Game of Life - Blinker

```
Initial:     Step 1:      Step 2:
. . . . .    . . . . .    . . . . .
. . ■ . .    . ■ ■ ■ .    . . ■ . .
. . ■ . .    . . . . .    . . ■ . .
. . ■ . .    . . . . .    . . ■ . .
. . . . .    . . . . .    . . . . .

Vertical → Horizontal → Vertical (period 2)
```

### Example 3: Langton's Ant

```
Legend: □=white, ■=black, ↑↓←→=ant

Initial (step 0):      Step 1:          Step 2:
□ □ □                  □ □ □            □ → □
□ ↑ □                  □ ■ → □          □ ■ □
□ □ □                  □ □ □            □ □ □

Ant on white: turn right, flip, move
Ant on black: turn left, flip, move

After ~10,000 steps: Creates "highway" pattern!
```

## 5. Complexity Analysis

| Automaton | Time per Step | Space | Notes |
|-----------|---------------|-------|-------|
| Elementary 1D | O(n) | O(n) | n = grid width |
| Game of Life | O(n²) | O(n²) | n = grid dimension |
| Hashlife | O(log t) | O(patterns) | Memoized |
| Langton's Ant | O(1) | O(visited) | Per step |

## 6. Visual Representation

### 6.1 Rule 30 Triangle

```
                    ■                    
                   ■■■                   
                  ■■  ■                  
                 ■■ ████                 
                ■■  ■   ■                
               ■■ ████ ■■■               
              ■■  ■    ■  ■              
             ■■ ████  ██████             
            ■■  ■   ■■■     ■            
           ■■ ████ ■■  ■   ■■■           
```

### 6.2 Game of Life Patterns

```
Still lifes:     Oscillators:     Spaceships:
                 
Block:           Blinker:         Glider:
■■               ■■■ ↔ ■          .■.
■■                   ■            ..■
                     ■            ■■■
                                  ↓ moves diagonally
```

### 6.3 Wolfram Classes

```
Class I (Rule 0):      Class II (Rule 4):
■■■■■■■■■■■■■         ■ ■ ■ ■ ■ ■ ■
■■■■■■■■■■■■■         ■ ■ ■ ■ ■ ■ ■
■■■■■■■■■■■■■         ■ ■ ■ ■ ■ ■ ■
(all same)             (periodic)

Class III (Rule 30):   Class IV (Rule 110):
■  ■■■ ■ ■■           ■■  ■ ■■■ ■
■■■   ■■■ ■           ■ ■■■   ■■■
■■ ■■■■ ■■■           ■■■ ■ ■■  ■
(chaotic)              (complex structures)
```

## 7. Implementation

```python
from typing import List, Tuple, Dict, Optional, Set
from dataclasses import dataclass
import numpy as np


def elementary_ca(
    size: int,
    rule: int,
    steps: int,
    initial: Optional[List[int]] = None
) -> List[List[int]]:
    """
    1D elementary cellular automaton.
    
    >>> history = elementary_ca(11, 30, 5)
    >>> len(history)
    6
    >>> history[0][5]  # Center cell
    1
    """
    # Initialize with single center cell
    if initial is None:
        current = [0] * size
        current[size // 2] = 1
    else:
        current = initial.copy()
    
    history = [current.copy()]
    
    for _ in range(steps):
        next_gen = [0] * size
        
        for i in range(size):
            # Get neighborhood (periodic boundary)
            left = current[(i - 1) % size]
            center = current[i]
            right = current[(i + 1) % size]
            
            # Calculate pattern index (0-7)
            pattern = left * 4 + center * 2 + right
            
            # Apply rule
            next_gen[i] = (rule >> pattern) & 1
        
        current = next_gen
        history.append(current.copy())
    
    return history


def render_1d_history(history: List[List[int]]) -> str:
    """
    Render 1D CA history as string.
    
    >>> history = elementary_ca(7, 30, 3)
    >>> rendered = render_1d_history(history)
    >>> '■' in rendered
    True
    """
    lines = []
    for row in history:
        line = ''.join('■' if cell else ' ' for cell in row)
        lines.append(line)
    return '\n'.join(lines)


class GameOfLife:
    """
    Conway's Game of Life implementation.
    
    >>> game = GameOfLife(10, 10)
    >>> game.set_pattern([(4,4), (4,5), (4,6)])  # Blinker
    >>> game.step()
    >>> game.get_alive_count()
    3
    """
    
    def __init__(self, rows: int, cols: int, wrap: bool = True):
        """
        Initialize Game of Life grid.
        
        Args:
            rows: Grid height
            cols: Grid width
            wrap: Toroidal boundary conditions
        """
        self.rows = rows
        self.cols = cols
        self.wrap = wrap
        self.grid = np.zeros((rows, cols), dtype=np.int8)
        self.generation = 0
    
    def set_cell(self, row: int, col: int, state: int = 1):
        """Set cell state."""
        self.grid[row % self.rows, col % self.cols] = state
    
    def set_pattern(self, cells: List[Tuple[int, int]]):
        """Set multiple cells alive."""
        for row, col in cells:
            self.set_cell(row, col, 1)
    
    def clear(self):
        """Clear the grid."""
        self.grid.fill(0)
        self.generation = 0
    
    def _count_neighbors(self, row: int, col: int) -> int:
        """Count alive neighbors."""
        count = 0
        for dr in (-1, 0, 1):
            for dc in (-1, 0, 1):
                if dr == 0 and dc == 0:
                    continue
                
                if self.wrap:
                    nr = (row + dr) % self.rows
                    nc = (col + dc) % self.cols
                else:
                    nr = row + dr
                    nc = col + dc
                    if not (0 <= nr < self.rows and 0 <= nc < self.cols):
                        continue
                
                count += self.grid[nr, nc]
        
        return count
    
    def step(self):
        """Advance one generation."""
        new_grid = np.zeros_like(self.grid)
        
        for row in range(self.rows):
            for col in range(self.cols):
                neighbors = self._count_neighbors(row, col)
                
                if self.grid[row, col] == 1:
                    # Alive cell survives with 2 or 3 neighbors
                    if neighbors in (2, 3):
                        new_grid[row, col] = 1
                else:
                    # Dead cell becomes alive with exactly 3 neighbors
                    if neighbors == 3:
                        new_grid[row, col] = 1
        
        self.grid = new_grid
        self.generation += 1
    
    def run(self, steps: int):
        """Run multiple generations."""
        for _ in range(steps):
            self.step()
    
    def get_alive_count(self) -> int:
        """Count alive cells."""
        return int(np.sum(self.grid))
    
    def get_pattern_hash(self) -> int:
        """Get hash of current pattern (for cycle detection)."""
        return hash(self.grid.tobytes())
    
    def render(self) -> str:
        """Render grid as string."""
        lines = []
        for row in self.grid:
            line = ''.join('■' if cell else '.' for cell in row)
            lines.append(line)
        return '\n'.join(lines)
    
    # Common patterns
    @classmethod
    def glider(cls) -> List[Tuple[int, int]]:
        """Return glider pattern."""
        return [(0, 1), (1, 2), (2, 0), (2, 1), (2, 2)]
    
    @classmethod
    def blinker(cls) -> List[Tuple[int, int]]:
        """Return blinker pattern."""
        return [(1, 0), (1, 1), (1, 2)]
    
    @classmethod
    def block(cls) -> List[Tuple[int, int]]:
        """Return block pattern (still life)."""
        return [(0, 0), (0, 1), (1, 0), (1, 1)]
    
    @classmethod
    def gosper_glider_gun(cls) -> List[Tuple[int, int]]:
        """Return Gosper glider gun pattern."""
        return [
            (5, 1), (5, 2), (6, 1), (6, 2),
            (5, 11), (6, 11), (7, 11), (4, 12), (8, 12),
            (3, 13), (9, 13), (3, 14), (9, 14), (6, 15),
            (4, 16), (8, 16), (5, 17), (6, 17), (7, 17),
            (6, 18), (3, 21), (4, 21), (5, 21), (3, 22),
            (4, 22), (5, 22), (2, 23), (6, 23), (1, 25),
            (2, 25), (6, 25), (7, 25), (3, 35), (4, 35),
            (3, 36), (4, 36)
        ]


class LangtonsAnt:
    """
    Langton's Ant turmite simulation.
    
    >>> ant = LangtonsAnt(50, 50)
    >>> ant.run(100)
    >>> ant.steps
    100
    """
    
    # Direction vectors: up, right, down, left
    DIRECTIONS = [(-1, 0), (0, 1), (1, 0), (0, -1)]
    
    def __init__(self, rows: int, cols: int):
        """Initialize Langton's Ant."""
        self.rows = rows
        self.cols = cols
        self.grid = np.zeros((rows, cols), dtype=np.int8)
        
        # Ant position and direction
        self.row = rows // 2
        self.col = cols // 2
        self.direction = 0  # 0=up, 1=right, 2=down, 3=left
        
        self.steps = 0
    
    def step(self):
        """Execute one step."""
        # Get current cell color
        current = self.grid[self.row, self.col]
        
        if current == 0:  # White
            self.direction = (self.direction + 1) % 4  # Turn right
            self.grid[self.row, self.col] = 1
        else:  # Black
            self.direction = (self.direction - 1) % 4  # Turn left
            self.grid[self.row, self.col] = 0
        
        # Move forward
        dr, dc = self.DIRECTIONS[self.direction]
        self.row = (self.row + dr) % self.rows
        self.col = (self.col + dc) % self.cols
        
        self.steps += 1
    
    def run(self, steps: int):
        """Run multiple steps."""
        for _ in range(steps):
            self.step()
    
    def get_black_count(self) -> int:
        """Count black cells."""
        return int(np.sum(self.grid))
    
    def render(self) -> str:
        """Render grid with ant position."""
        lines = []
        arrow = ['↑', '→', '↓', '←'][self.direction]
        
        for r in range(self.rows):
            line = []
            for c in range(self.cols):
                if r == self.row and c == self.col:
                    line.append(arrow)
                elif self.grid[r, c]:
                    line.append('■')
                else:
                    line.append('.')
            lines.append(''.join(line))
        
        return '\n'.join(lines)


class Wireworld:
    """
    Wireworld cellular automaton for circuit simulation.
    
    States: Empty(0), Wire(1), Electron Head(2), Electron Tail(3)
    """
    
    EMPTY = 0
    WIRE = 1
    HEAD = 2
    TAIL = 3
    
    def __init__(self, rows: int, cols: int):
        """Initialize Wireworld grid."""
        self.rows = rows
        self.cols = cols
        self.grid = np.zeros((rows, cols), dtype=np.int8)
    
    def set_wire(self, cells: List[Tuple[int, int]]):
        """Set cells as wire."""
        for r, c in cells:
            self.grid[r, c] = self.WIRE
    
    def set_electron(self, row: int, col: int):
        """Place electron head at position."""
        self.grid[row, col] = self.HEAD
    
    def _count_heads(self, row: int, col: int) -> int:
        """Count electron head neighbors."""
        count = 0
        for dr in (-1, 0, 1):
            for dc in (-1, 0, 1):
                if dr == 0 and dc == 0:
                    continue
                nr = row + dr
                nc = col + dc
                if 0 <= nr < self.rows and 0 <= nc < self.cols:
                    if self.grid[nr, nc] == self.HEAD:
                        count += 1
        return count
    
    def step(self):
        """Advance one step."""
        new_grid = np.zeros_like(self.grid)
        
        for r in range(self.rows):
            for c in range(self.cols):
                state = self.grid[r, c]
                
                if state == self.EMPTY:
                    new_grid[r, c] = self.EMPTY
                elif state == self.HEAD:
                    new_grid[r, c] = self.TAIL
                elif state == self.TAIL:
                    new_grid[r, c] = self.WIRE
                elif state == self.WIRE:
                    heads = self._count_heads(r, c)
                    if heads in (1, 2):
                        new_grid[r, c] = self.HEAD
                    else:
                        new_grid[r, c] = self.WIRE
        
        self.grid = new_grid
    
    def render(self) -> str:
        """Render grid."""
        symbols = {0: '.', 1: '─', 2: '●', 3: '○'}
        lines = []
        for row in self.grid:
            line = ''.join(symbols[cell] for cell in row)
            lines.append(line)
        return '\n'.join(lines)


def totalistic_ca(
    size: int,
    rule: int,
    steps: int,
    states: int = 3
) -> List[List[int]]:
    """
    Totalistic cellular automaton (based on sum of neighbors).
    
    >>> history = totalistic_ca(15, 777, 7, states=3)
    >>> len(history)
    8
    """
    # Rule is encoded as base-k digits for each sum
    max_sum = states * 3 - 2  # Max possible sum with 3 neighbors
    
    current = [0] * size
    current[size // 2] = states - 1  # Center cell = max state
    
    history = [current.copy()]
    
    for _ in range(steps):
        next_gen = [0] * size
        
        for i in range(size):
            # Sum of neighborhood
            total = (current[(i-1) % size] + 
                    current[i] + 
                    current[(i+1) % size])
            
            # Look up in rule (base-k encoding)
            next_gen[i] = (rule // (states ** total)) % states
        
        current = next_gen
        history.append(current.copy())
    
    return history


# Demo
if __name__ == "__main__":
    print("Cellular Automata Demo")
    print("=" * 50)
    
    # Elementary CA - Rule 30
    print("\n1. Rule 30 (Elementary CA):")
    history = elementary_ca(41, 30, 15)
    print(render_1d_history(history[-10:]))
    
    # Rule 110 (Turing complete)
    print("\n2. Rule 110:")
    history = elementary_ca(41, 110, 15)
    print(render_1d_history(history[-10:]))
    
    # Game of Life
    print("\n3. Game of Life - Blinker:")
    game = GameOfLife(5, 5)
    game.set_pattern([(2, 1), (2, 2), (2, 3)])
    
    for i in range(3):
        print(f"   Generation {i}:")
        for line in game.render().split('\n'):
            print(f"   {line}")
        game.step()
    
    # Langton's Ant
    print("\n4. Langton's Ant (after 100 steps):")
    ant = LangtonsAnt(15, 15)
    ant.run(100)
    print(f"   Black cells: {ant.get_black_count()}")
    print(f"   Ant position: ({ant.row}, {ant.col})")
    
    # Wireworld
    print("\n5. Wireworld:")
    wire = Wireworld(5, 10)
    wire.set_wire([(2, c) for c in range(10)])  # Horizontal wire
    wire.set_electron(2, 1)  # Place electron
    
    for i in range(4):
        print(f"   Step {i}:")
        for line in wire.render().split('\n'):
            print(f"   {line}")
        wire.step()
```

## 8. Applications

### 8.1 Scientific Modeling
- Traffic flow simulation
- Forest fire spread
- Crystal growth
- Epidemic modeling

### 8.2 Computer Science
- Random number generation (Rule 30)
- Turing machines (Rule 110)
- Parallel computation models

### 8.3 Art and Design
- Procedural generation
- Pattern creation
- Music composition

## 9. Real-World Software Engineering Applications

### 9.1 Production Example: Procedural World Generator

```python
from typing import Dict, List, Tuple, Optional
from dataclasses import dataclass
from enum import IntEnum
import numpy as np
import random


class Terrain(IntEnum):
    """Terrain types for world generation."""
    WATER = 0
    SAND = 1
    GRASS = 2
    FOREST = 3
    MOUNTAIN = 4
    SNOW = 5


@dataclass
class WorldConfig:
    """Configuration for world generation."""
    width: int = 100
    height: int = 100
    sea_level: float = 0.4
    mountain_level: float = 0.75
    snow_level: float = 0.9
    initial_land_chance: float = 0.45
    smoothing_iterations: int = 5


class CaveGenerator:
    """
    Generate cave systems using cellular automata.
    
    Uses birth/death rules similar to Game of Life.
    """
    
    def __init__(
        self,
        width: int,
        height: int,
        fill_chance: float = 0.45,
        birth_limit: int = 4,
        death_limit: int = 3
    ):
        """
        Initialize cave generator.
        
        Args:
            width: Cave width
            height: Cave height
            fill_chance: Initial wall probability
            birth_limit: Neighbors needed for wall birth
            death_limit: Neighbors needed for wall death
        """
        self.width = width
        self.height = height
        self.fill_chance = fill_chance
        self.birth_limit = birth_limit
        self.death_limit = death_limit
        
        self.grid = np.zeros((height, width), dtype=np.int8)
    
    def initialize(self, seed: Optional[int] = None):
        """Initialize random cave."""
        if seed is not None:
            random.seed(seed)
        
        for y in range(self.height):
            for x in range(self.width):
                # Edges are always walls
                if x == 0 or x == self.width - 1 or \
                   y == 0 or y == self.height - 1:
                    self.grid[y, x] = 1
                elif random.random() < self.fill_chance:
                    self.grid[y, x] = 1
    
    def _count_neighbors(self, x: int, y: int) -> int:
        """Count wall neighbors."""
        count = 0
        for dy in range(-1, 2):
            for dx in range(-1, 2):
                if dx == 0 and dy == 0:
                    continue
                nx, ny = x + dx, y + dy
                if 0 <= nx < self.width and 0 <= ny < self.height:
                    count += self.grid[ny, nx]
                else:
                    count += 1  # Out of bounds counts as wall
        return count
    
    def step(self):
        """Apply one CA step."""
        new_grid = np.copy(self.grid)
        
        for y in range(1, self.height - 1):
            for x in range(1, self.width - 1):
                neighbors = self._count_neighbors(x, y)
                
                if self.grid[y, x] == 1:
                    # Wall dies if too few neighbors
                    if neighbors < self.death_limit:
                        new_grid[y, x] = 0
                else:
                    # Empty becomes wall if enough neighbors
                    if neighbors > self.birth_limit:
                        new_grid[y, x] = 1
        
        self.grid = new_grid
    
    def generate(self, steps: int = 5) -> np.ndarray:
        """Generate complete cave."""
        self.initialize()
        for _ in range(steps):
            self.step()
        return self.grid
    
    def flood_fill(self, start_x: int, start_y: int) -> List[Tuple[int, int]]:
        """Find connected region from start point."""
        if self.grid[start_y, start_x] == 1:
            return []
        
        visited = set()
        stack = [(start_x, start_y)]
        region = []
        
        while stack:
            x, y = stack.pop()
            if (x, y) in visited:
                continue
            if x < 0 or x >= self.width or y < 0 or y >= self.height:
                continue
            if self.grid[y, x] == 1:
                continue
            
            visited.add((x, y))
            region.append((x, y))
            
            stack.extend([
                (x+1, y), (x-1, y), (x, y+1), (x, y-1)
            ])
        
        return region
    
    def get_largest_cave(self) -> np.ndarray:
        """Return mask of largest connected cave."""
        visited = set()
        largest = []
        
        for y in range(self.height):
            for x in range(self.width):
                if (x, y) not in visited and self.grid[y, x] == 0:
                    region = self.flood_fill(x, y)
                    visited.update(region)
                    if len(region) > len(largest):
                        largest = region
        
        mask = np.zeros_like(self.grid)
        for x, y in largest:
            mask[y, x] = 1
        
        return mask


class TerrainGenerator:
    """
    Generate terrain using multi-layer cellular automata.
    """
    
    def __init__(self, config: WorldConfig):
        self.config = config
        self.heightmap = np.zeros(
            (config.height, config.width), 
            dtype=np.float32
        )
        self.terrain = np.zeros(
            (config.height, config.width), 
            dtype=np.int8
        )
    
    def _smooth_heightmap(self, iterations: int = 1):
        """Apply smoothing to heightmap."""
        for _ in range(iterations):
            new_map = np.copy(self.heightmap)
            
            for y in range(1, self.config.height - 1):
                for x in range(1, self.config.width - 1):
                    # Average of neighbors
                    total = 0
                    count = 0
                    for dy in range(-1, 2):
                        for dx in range(-1, 2):
                            total += self.heightmap[y + dy, x + dx]
                            count += 1
                    new_map[y, x] = total / count
            
            self.heightmap = new_map
    
    def generate_heightmap(self, seed: Optional[int] = None):
        """Generate terrain heightmap using CA."""
        if seed is not None:
            np.random.seed(seed)
        
        # Initial random land/water
        self.heightmap = np.random.random(
            (self.config.height, self.config.width)
        ).astype(np.float32)
        
        # Apply CA-like smoothing
        self._smooth_heightmap(self.config.smoothing_iterations)
        
        # Normalize to 0-1
        self.heightmap = (self.heightmap - self.heightmap.min()) / \
                         (self.heightmap.max() - self.heightmap.min())
    
    def classify_terrain(self):
        """Convert heightmap to terrain types."""
        for y in range(self.config.height):
            for x in range(self.config.width):
                h = self.heightmap[y, x]
                
                if h < self.config.sea_level:
                    self.terrain[y, x] = Terrain.WATER
                elif h < self.config.sea_level + 0.05:
                    self.terrain[y, x] = Terrain.SAND
                elif h < self.config.mountain_level:
                    # Determine grass vs forest based on neighbors
                    self.terrain[y, x] = Terrain.GRASS
                elif h < self.config.snow_level:
                    self.terrain[y, x] = Terrain.MOUNTAIN
                else:
                    self.terrain[y, x] = Terrain.SNOW
    
    def grow_forests(self, iterations: int = 3):
        """Use CA to grow forests on grass."""
        for _ in range(iterations):
            new_terrain = np.copy(self.terrain)
            
            for y in range(1, self.config.height - 1):
                for x in range(1, self.config.width - 1):
                    if self.terrain[y, x] != Terrain.GRASS:
                        continue
                    
                    # Count forest neighbors
                    forest_count = 0
                    for dy in range(-1, 2):
                        for dx in range(-1, 2):
                            if self.terrain[y+dy, x+dx] == Terrain.FOREST:
                                forest_count += 1
                    
                    # Grass becomes forest with enough neighbors
                    if forest_count >= 3 or \
                       (forest_count >= 1 and random.random() < 0.1):
                        new_terrain[y, x] = Terrain.FOREST
            
            self.terrain = new_terrain
    
    def generate(self, seed: Optional[int] = None) -> np.ndarray:
        """Generate complete terrain."""
        self.generate_heightmap(seed)
        self.classify_terrain()
        
        # Seed some forests
        forest_seeds = np.random.random(
            (self.config.height, self.config.width)
        ) < 0.1
        self.terrain[(self.terrain == Terrain.GRASS) & forest_seeds] = \
            Terrain.FOREST
        
        self.grow_forests()
        
        return self.terrain
    
    def render(self) -> str:
        """Render terrain as string."""
        symbols = {
            Terrain.WATER: '~',
            Terrain.SAND: '.',
            Terrain.GRASS: ',',
            Terrain.FOREST: '♣',
            Terrain.MOUNTAIN: '▲',
            Terrain.SNOW: '△'
        }
        
        lines = []
        for row in self.terrain:
            line = ''.join(symbols[Terrain(cell)] for cell in row)
            lines.append(line)
        return '\n'.join(lines)
    
    def get_statistics(self) -> Dict[str, float]:
        """Get terrain statistics."""
        total = self.config.width * self.config.height
        stats = {}
        
        for terrain_type in Terrain:
            count = np.sum(self.terrain == terrain_type)
            stats[terrain_type.name] = count / total
        
        return stats


class PatternLibrary:
    """
    Library of Game of Life patterns for use in games/simulations.
    """
    
    PATTERNS = {
        'glider': [
            (0, 1), (1, 2), (2, 0), (2, 1), (2, 2)
        ],
        'blinker': [
            (1, 0), (1, 1), (1, 2)
        ],
        'toad': [
            (1, 1), (1, 2), (1, 3),
            (2, 0), (2, 1), (2, 2)
        ],
        'beacon': [
            (0, 0), (0, 1), (1, 0),
            (2, 3), (3, 2), (3, 3)
        ],
        'pulsar': [
            # Complex 13-period oscillator
            (2, 4), (2, 5), (2, 6), (2, 10), (2, 11), (2, 12),
            (4, 2), (4, 7), (4, 9), (4, 14),
            (5, 2), (5, 7), (5, 9), (5, 14),
            (6, 2), (6, 7), (6, 9), (6, 14),
            (7, 4), (7, 5), (7, 6), (7, 10), (7, 11), (7, 12),
            (9, 4), (9, 5), (9, 6), (9, 10), (9, 11), (9, 12),
            (10, 2), (10, 7), (10, 9), (10, 14),
            (11, 2), (11, 7), (11, 9), (11, 14),
            (12, 2), (12, 7), (12, 9), (12, 14),
            (14, 4), (14, 5), (14, 6), (14, 10), (14, 11), (14, 12),
        ],
        'spaceship_lwss': [
            (0, 1), (0, 4),
            (1, 0),
            (2, 0), (2, 4),
            (3, 0), (3, 1), (3, 2), (3, 3)
        ]
    }
    
    @classmethod
    def get_pattern(
        cls, 
        name: str, 
        offset: Tuple[int, int] = (0, 0)
    ) -> List[Tuple[int, int]]:
        """Get pattern with optional offset."""
        if name not in cls.PATTERNS:
            raise ValueError(f"Unknown pattern: {name}")
        
        dr, dc = offset
        return [(r + dr, c + dc) for r, c in cls.PATTERNS[name]]
    
    @classmethod
    def list_patterns(cls) -> List[str]:
        """List available patterns."""
        return list(cls.PATTERNS.keys())


# Demo
if __name__ == "__main__":
    print("Procedural World Generator Demo")
    print("=" * 50)
    
    # Cave generation
    print("\n1. Cave Generation:")
    cave_gen = CaveGenerator(40, 20)
    cave = cave_gen.generate(steps=5)
    
    for row in cave:
        print(''.join(['█' if c else ' ' for c in row]))
    
    largest = cave_gen.get_largest_cave()
    print(f"\n   Largest cave size: {np.sum(largest)} cells")
    
    # Terrain generation
    print("\n2. Terrain Generation:")
    config = WorldConfig(width=60, height=20)
    terrain_gen = TerrainGenerator(config)
    terrain = terrain_gen.generate(seed=42)
    
    print(terrain_gen.render())
    print(f"\n   Statistics: {terrain_gen.get_statistics()}")
    
    # Pattern library
    print("\n3. Pattern Library:")
    print(f"   Available patterns: {PatternLibrary.list_patterns()}")
    glider = PatternLibrary.get_pattern('glider', offset=(5, 5))
    print(f"   Glider at (5,5): {glider}")
```

## 10. Comparison

| Automaton | Dimensions | States | Complexity | Behavior |
|-----------|------------|--------|------------|----------|
| Elementary | 1D | 2 | Simple | Varied |
| Game of Life | 2D | 2 | Medium | Complex |
| Langton's Ant | 2D | 2 | Simple | Emergent |
| Wireworld | 2D | 4 | Medium | Computational |
| Rule 110 | 1D | 2 | Simple | Turing complete |

## 11. Best Practices

1. **Use efficient boundary handling**: Toroidal vs fixed
2. **Consider double buffering**: Avoid in-place updates
3. **Optimize for patterns**: HashLife for Game of Life
4. **Tune parameters**: CA behavior is sensitive
5. **Visualize evolution**: Debug with history

## 12. References

- [Wikipedia: Cellular Automaton](https://en.wikipedia.org/wiki/Cellular_automaton)
- Wolfram, S. "A New Kind of Science"
- [LifeWiki](https://conwaylife.com/wiki/)
- [Elementary CA](https://mathworld.wolfram.com/ElementaryCellularAutomaton.html)
