# Rat in a Maze

## Overview
- **Category**: Backtracking / Path Finding
- **Complexity**: Time: O(2^(n²)) | Space: O(n²)
- **Type**: Constraint satisfaction, path finding
- **Source Files**: [backtracking/rat_in_maze.py](../../../backtracking/rat_in_maze.py)

## 1. Mathematical Foundation

### 1.1 Problem Definition

Given an $n \times n$ binary maze:
- 1 = passable cell
- 0 = blocked cell

Find a path from $(0, 0)$ to $(n-1, n-1)$ moving only down (D) or right (R).

### 1.2 Solution Space

A path of length $2(n-1)$ consists of:
- Exactly $n-1$ moves right (R)
- Exactly $n-1$ moves down (D)

Without obstacles, total paths:
$$
\binom{2(n-1)}{n-1} = \frac{(2n-2)!}{((n-1)!)^2}
$$

### 1.3 State Space Tree

Each node represents a position $(i, j)$. Branches represent valid moves.

For a 3×3 maze:
```
          (0,0)
         /     \
      (0,1)   (1,0)
       /  \    /  \
    (0,2)(1,1)(1,1)(2,0)
     ...     ...    ...
```

### 1.4 Constraint Satisfaction

At each position $(i, j)$:
$$
\text{valid}(i, j) = \begin{cases}
\text{true} & \text{if } 0 \leq i < n, 0 \leq j < n, \text{maze}[i][j] = 1 \\
\text{false} & \text{otherwise}
\end{cases}
$$

### 1.5 Variants

| Variant | Allowed Moves | Applications |
|---------|---------------|--------------|
| 2-direction | D, R | Simple mazes |
| 4-direction | U, D, L, R | General mazes |
| 8-direction | All diagonal + cardinal | Chess problems |

## 2. Algorithm Variants

### 2.1 Basic Backtracking
Only down and right moves.

### 2.2 Four-Direction
Up, down, left, right with cycle detection.

### 2.3 All Paths
Find all possible paths, not just one.

### 2.4 Shortest Path
BFS for unweighted, Dijkstra for weighted.

## 3. Pseudocode

### 3.1 Two-Direction (D/R Only)

```
ALGORITHM SolveMaze(maze, n)
    INPUT: n×n maze (1=open, 0=blocked)
    OUTPUT: Path from (0,0) to (n-1,n-1) or None
    
    solution ← n×n matrix of zeros
    
    if SolveMazeUtil(maze, 0, 0, solution, n) then
        return solution
    else
        return None

ALGORITHM SolveMazeUtil(maze, x, y, solution, n)
    // Base case: reached destination
    if x = n-1 AND y = n-1 then
        solution[x][y] ← 1
        return true
    
    // Check if current position is valid
    if IsValid(maze, x, y, n) then
        // Mark current cell in solution
        solution[x][y] ← 1
        
        // Try moving right
        if SolveMazeUtil(maze, x, y+1, solution, n) then
            return true
        
        // Try moving down
        if SolveMazeUtil(maze, x+1, y, solution, n) then
            return true
        
        // Backtrack: unmark current cell
        solution[x][y] ← 0
    
    return false

ALGORITHM IsValid(maze, x, y, n)
    return 0 ≤ x < n AND 0 ≤ y < n AND maze[x][y] = 1
```

### 3.2 Four-Direction with Cycle Detection

```
ALGORITHM SolveMaze4Dir(maze, n)
    INPUT: n×n maze
    OUTPUT: Path as string of moves
    
    visited ← n×n matrix of false
    path ← []
    
    if Solve4DirUtil(maze, 0, 0, visited, path, n) then
        return path
    else
        return None

ALGORITHM Solve4DirUtil(maze, x, y, visited, path, n)
    // Check destination
    if x = n-1 AND y = n-1 then
        return true
    
    // Mark as visited
    visited[x][y] ← true
    
    // Define moves: Down, Left, Right, Up (lexicographic order)
    moves ← [(1,0,'D'), (0,-1,'L'), (0,1,'R'), (-1,0,'U')]
    
    for each (dx, dy, direction) in moves do
        newX ← x + dx
        newY ← y + dy
        
        if IsValid(maze, newX, newY, n) AND NOT visited[newX][newY] then
            path.append(direction)
            
            if Solve4DirUtil(maze, newX, newY, visited, path, n) then
                return true
            
            path.pop()  // Backtrack
    
    visited[x][y] ← false  // Unmark for other paths
    return false
```

### 3.3 Find All Paths

```
ALGORITHM FindAllPaths(maze, n)
    INPUT: n×n maze
    OUTPUT: List of all valid paths
    
    all_paths ← []
    visited ← n×n matrix of false
    
    FindAllPathsUtil(maze, 0, 0, visited, [], all_paths, n)
    return all_paths

ALGORITHM FindAllPathsUtil(maze, x, y, visited, current_path, all_paths, n)
    if x = n-1 AND y = n-1 then
        all_paths.append(current_path.copy())
        return
    
    visited[x][y] ← true
    
    moves ← [(1,0,'D'), (0,-1,'L'), (0,1,'R'), (-1,0,'U')]
    
    for each (dx, dy, direction) in moves do
        newX ← x + dx
        newY ← y + dy
        
        if IsValid(maze, newX, newY, n) AND NOT visited[newX][newY] then
            current_path.append(direction)
            FindAllPathsUtil(maze, newX, newY, visited, current_path, all_paths, n)
            current_path.pop()
    
    visited[x][y] ← false
```

## 4. Step-by-Step Example

### Example: 4×4 Maze

```
Maze:
  1 0 0 0
  1 1 0 1
  0 1 0 0
  1 1 1 1

Start: (0,0), End: (3,3)
```

### Execution Trace

```
Step 1: Start at (0,0)
  Solution:     Visited:
  [1,0,0,0]     [T,F,F,F]
  [0,0,0,0]     [F,F,F,F]
  [0,0,0,0]     [F,F,F,F]
  [0,0,0,0]     [F,F,F,F]
  
  Try Right → (0,1): BLOCKED (maze[0][1]=0)
  Try Down → (1,0): VALID

Step 2: At (1,0)
  Solution:     
  [1,0,0,0]     
  [1,0,0,0]     
  [0,0,0,0]     
  [0,0,0,0]     
  
  Try Right → (1,1): VALID

Step 3: At (1,1)
  Solution:     
  [1,0,0,0]     
  [1,1,0,0]     
  [0,0,0,0]     
  [0,0,0,0]     
  
  Try Right → (1,2): BLOCKED
  Try Down → (2,1): VALID

Step 4: At (2,1)
  Solution:     
  [1,0,0,0]     
  [1,1,0,0]     
  [0,1,0,0]     
  [0,0,0,0]     
  
  Try Right → (2,2): BLOCKED
  Try Down → (3,1): VALID

Step 5: At (3,1)
  Solution:     
  [1,0,0,0]     
  [1,1,0,0]     
  [0,1,0,0]     
  [0,1,0,0]     
  
  Try Right → (3,2): VALID

Step 6: At (3,2)
  Solution:     
  [1,0,0,0]     
  [1,1,0,0]     
  [0,1,0,0]     
  [0,1,1,0]     
  
  Try Right → (3,3): VALID → DESTINATION!

Final Solution:
  [1,0,0,0]
  [1,1,0,0]
  [0,1,0,0]
  [0,1,1,1]

Path: D → R → D → D → R → R
String: "DRDDРR"
```

## 5. Complexity Analysis

| Metric | Complexity |
|--------|------------|
| Time (worst) | O(2^(n²)) |
| Time (2-dir) | O(2^(2n-2)) |
| Space | O(n²) |
| Recursion depth | O(n²) |

**Note**: With good pruning, practical performance is much better.

## 6. Visual Representation

### 6.1 Maze Solving Visualization

```
Initial Maze (4×4):          Solution Path:
┌───┬───┬───┬───┐           ┌───┬───┬───┬───┐
│ 1 │ 0 │ 0 │ 0 │           │ ★ │   │   │   │
├───┼───┼───┼───┤           ├───┼───┼───┼───┤
│ 1 │ 1 │ 0 │ 1 │    →      │ ↓ │ → │   │   │
├───┼───┼───┼───┤           ├───┼───┼───┼───┤
│ 0 │ 1 │ 0 │ 0 │           │   │ ↓ │   │   │
├───┼───┼───┼───┤           ├───┼───┼───┼───┤
│ 1 │ 1 │ 1 │ 1 │           │   │ → │ → │ ◎ │
└───┴───┴───┴───┘           └───┴───┴───┴───┘

Legend: ★=Start, ◎=End, 0=Blocked, 1=Open
```

### 6.2 Backtracking Tree

```
                    (0,0)
                      │
                    (1,0) D
                      │
                    (1,1) R
                   /      \
              (1,2)✗      (2,1) D
                            │
                          (3,1) D
                            │
                          (3,2) R
                            │
                          (3,3) R ✓
```

## 7. Implementation

```python
from typing import List, Optional, Tuple
from dataclasses import dataclass
from collections import deque


def solve_maze_2dir(maze: List[List[int]]) -> Optional[List[List[int]]]:
    """
    Solve maze using only Down and Right moves.
    
    >>> maze = [[1, 0], [1, 1]]
    >>> solve_maze_2dir(maze)
    [[1, 0], [1, 1]]
    """
    n = len(maze)
    if n == 0 or maze[0][0] == 0 or maze[n-1][n-1] == 0:
        return None
    
    solution = [[0] * n for _ in range(n)]
    
    if _solve_2dir(maze, 0, 0, solution, n):
        return solution
    return None


def _solve_2dir(
    maze: List[List[int]],
    x: int,
    y: int,
    solution: List[List[int]],
    n: int
) -> bool:
    """Backtracking helper for 2-direction maze."""
    # Destination reached
    if x == n - 1 and y == n - 1:
        solution[x][y] = 1
        return True
    
    # Check bounds and obstacles
    if 0 <= x < n and 0 <= y < n and maze[x][y] == 1:
        solution[x][y] = 1
        
        # Try right
        if _solve_2dir(maze, x, y + 1, solution, n):
            return True
        
        # Try down
        if _solve_2dir(maze, x + 1, y, solution, n):
            return True
        
        # Backtrack
        solution[x][y] = 0
    
    return False


def solve_maze_4dir(maze: List[List[int]]) -> Optional[str]:
    """
    Solve maze with all 4 directions, return path string.
    
    >>> maze = [[1, 0, 0], [1, 1, 0], [0, 1, 1]]
    >>> solve_maze_4dir(maze)
    'DDRR'
    """
    n = len(maze)
    if n == 0 or maze[0][0] == 0 or maze[n-1][n-1] == 0:
        return None
    
    visited = [[False] * n for _ in range(n)]
    path = []
    
    if _solve_4dir(maze, 0, 0, visited, path, n):
        return ''.join(path)
    return None


def _solve_4dir(
    maze: List[List[int]],
    x: int,
    y: int,
    visited: List[List[bool]],
    path: List[str],
    n: int
) -> bool:
    """Backtracking helper for 4-direction maze."""
    if x == n - 1 and y == n - 1:
        return True
    
    visited[x][y] = True
    
    # Moves in lexicographic order: D, L, R, U
    moves = [(1, 0, 'D'), (0, -1, 'L'), (0, 1, 'R'), (-1, 0, 'U')]
    
    for dx, dy, direction in moves:
        nx, ny = x + dx, y + dy
        
        if 0 <= nx < n and 0 <= ny < n and maze[nx][ny] == 1 and not visited[nx][ny]:
            path.append(direction)
            
            if _solve_4dir(maze, nx, ny, visited, path, n):
                return True
            
            path.pop()
    
    visited[x][y] = False
    return False


def find_all_paths(maze: List[List[int]]) -> List[str]:
    """
    Find all paths from top-left to bottom-right.
    
    >>> maze = [[1, 1], [1, 1]]
    >>> sorted(find_all_paths(maze))
    ['DR', 'RD']
    """
    n = len(maze)
    if n == 0 or maze[0][0] == 0 or maze[n-1][n-1] == 0:
        return []
    
    all_paths = []
    visited = [[False] * n for _ in range(n)]
    
    _find_all_paths(maze, 0, 0, visited, [], all_paths, n)
    return all_paths


def _find_all_paths(
    maze: List[List[int]],
    x: int,
    y: int,
    visited: List[List[bool]],
    current: List[str],
    all_paths: List[str],
    n: int
) -> None:
    """Backtracking to find all paths."""
    if x == n - 1 and y == n - 1:
        all_paths.append(''.join(current))
        return
    
    visited[x][y] = True
    
    moves = [(1, 0, 'D'), (0, -1, 'L'), (0, 1, 'R'), (-1, 0, 'U')]
    
    for dx, dy, direction in moves:
        nx, ny = x + dx, y + dy
        
        if 0 <= nx < n and 0 <= ny < n and maze[nx][ny] == 1 and not visited[nx][ny]:
            current.append(direction)
            _find_all_paths(maze, nx, ny, visited, current, all_paths, n)
            current.pop()
    
    visited[x][y] = False


def shortest_path_bfs(maze: List[List[int]]) -> Optional[Tuple[int, str]]:
    """
    Find shortest path using BFS.
    
    Returns (length, path_string) or None.
    
    >>> maze = [[1, 1, 1], [0, 0, 1], [1, 1, 1]]
    >>> shortest_path_bfs(maze)
    (4, 'RRDD')
    """
    n = len(maze)
    if n == 0 or maze[0][0] == 0 or maze[n-1][n-1] == 0:
        return None
    
    # BFS with path tracking
    queue = deque([(0, 0, "")])
    visited = [[False] * n for _ in range(n)]
    visited[0][0] = True
    
    moves = [(1, 0, 'D'), (0, -1, 'L'), (0, 1, 'R'), (-1, 0, 'U')]
    
    while queue:
        x, y, path = queue.popleft()
        
        if x == n - 1 and y == n - 1:
            return (len(path), path)
        
        for dx, dy, direction in moves:
            nx, ny = x + dx, y + dy
            
            if 0 <= nx < n and 0 <= ny < n and maze[nx][ny] == 1 and not visited[nx][ny]:
                visited[nx][ny] = True
                queue.append((nx, ny, path + direction))
    
    return None


@dataclass
class MazeResult:
    """Result of maze solving."""
    solvable: bool
    path: Optional[str]
    path_length: int
    cells_explored: int
    solution_matrix: Optional[List[List[int]]]


class MazeSolver:
    """
    Comprehensive maze solver with multiple algorithms.
    """
    
    def __init__(self, maze: List[List[int]]):
        """
        Initialize with maze.
        
        >>> solver = MazeSolver([[1, 1], [1, 1]])
        >>> solver.n
        2
        """
        self.maze = maze
        self.n = len(maze)
        self.cells_explored = 0
    
    def solve(
        self,
        method: str = "backtrack",
        all_paths: bool = False
    ) -> MazeResult:
        """
        Solve the maze using specified method.
        
        Args:
            method: "backtrack", "bfs", or "dfs"
            all_paths: If True, find all paths
        """
        if self.n == 0:
            return MazeResult(False, None, 0, 0, None)
        
        if self.maze[0][0] == 0 or self.maze[self.n-1][self.n-1] == 0:
            return MazeResult(False, None, 0, 0, None)
        
        self.cells_explored = 0
        
        if all_paths:
            paths = self._find_all_paths()
            if paths:
                # Return shortest path info
                shortest = min(paths, key=len)
                return MazeResult(
                    True, shortest, len(shortest),
                    self.cells_explored, None
                )
            return MazeResult(False, None, 0, self.cells_explored, None)
        
        if method == "bfs":
            result = self._solve_bfs()
        else:
            result = self._solve_backtrack()
        
        return result
    
    def _solve_backtrack(self) -> MazeResult:
        """Solve using backtracking."""
        visited = [[False] * self.n for _ in range(self.n)]
        solution = [[0] * self.n for _ in range(self.n)]
        path = []
        
        success = self._backtrack(0, 0, visited, solution, path)
        
        return MazeResult(
            success,
            ''.join(path) if success else None,
            len(path) if success else 0,
            self.cells_explored,
            solution if success else None
        )
    
    def _backtrack(
        self,
        x: int,
        y: int,
        visited: List[List[bool]],
        solution: List[List[int]],
        path: List[str]
    ) -> bool:
        """Backtracking helper."""
        self.cells_explored += 1
        
        if x == self.n - 1 and y == self.n - 1:
            solution[x][y] = 1
            return True
        
        visited[x][y] = True
        solution[x][y] = 1
        
        moves = [(1, 0, 'D'), (0, 1, 'R'), (0, -1, 'L'), (-1, 0, 'U')]
        
        for dx, dy, direction in moves:
            nx, ny = x + dx, y + dy
            
            if (0 <= nx < self.n and 0 <= ny < self.n and 
                self.maze[nx][ny] == 1 and not visited[nx][ny]):
                
                path.append(direction)
                
                if self._backtrack(nx, ny, visited, solution, path):
                    return True
                
                path.pop()
        
        visited[x][y] = False
        solution[x][y] = 0
        return False
    
    def _solve_bfs(self) -> MazeResult:
        """Solve using BFS (shortest path)."""
        queue = deque([(0, 0, "")])
        visited = [[False] * self.n for _ in range(self.n)]
        visited[0][0] = True
        
        moves = [(1, 0, 'D'), (0, 1, 'R'), (0, -1, 'L'), (-1, 0, 'U')]
        
        while queue:
            x, y, path = queue.popleft()
            self.cells_explored += 1
            
            if x == self.n - 1 and y == self.n - 1:
                # Build solution matrix
                solution = self._build_solution_matrix(path)
                return MazeResult(True, path, len(path), self.cells_explored, solution)
            
            for dx, dy, direction in moves:
                nx, ny = x + dx, y + dy
                
                if (0 <= nx < self.n and 0 <= ny < self.n and 
                    self.maze[nx][ny] == 1 and not visited[nx][ny]):
                    visited[nx][ny] = True
                    queue.append((nx, ny, path + direction))
        
        return MazeResult(False, None, 0, self.cells_explored, None)
    
    def _find_all_paths(self) -> List[str]:
        """Find all paths."""
        all_paths = []
        visited = [[False] * self.n for _ in range(self.n)]
        
        self._all_paths_helper(0, 0, visited, [], all_paths)
        return all_paths
    
    def _all_paths_helper(
        self,
        x: int,
        y: int,
        visited: List[List[bool]],
        current: List[str],
        all_paths: List[str]
    ) -> None:
        """Helper for finding all paths."""
        self.cells_explored += 1
        
        if x == self.n - 1 and y == self.n - 1:
            all_paths.append(''.join(current))
            return
        
        visited[x][y] = True
        
        moves = [(1, 0, 'D'), (0, 1, 'R'), (0, -1, 'L'), (-1, 0, 'U')]
        
        for dx, dy, direction in moves:
            nx, ny = x + dx, y + dy
            
            if (0 <= nx < self.n and 0 <= ny < self.n and 
                self.maze[nx][ny] == 1 and not visited[nx][ny]):
                current.append(direction)
                self._all_paths_helper(nx, ny, visited, current, all_paths)
                current.pop()
        
        visited[x][y] = False
    
    def _build_solution_matrix(self, path: str) -> List[List[int]]:
        """Build solution matrix from path string."""
        solution = [[0] * self.n for _ in range(self.n)]
        x, y = 0, 0
        solution[0][0] = 1
        
        moves = {'D': (1, 0), 'U': (-1, 0), 'R': (0, 1), 'L': (0, -1)}
        
        for direction in path:
            dx, dy = moves[direction]
            x, y = x + dx, y + dy
            solution[x][y] = 1
        
        return solution
    
    def visualize(self, solution: Optional[List[List[int]]] = None) -> str:
        """
        Visualize maze with optional solution path.
        """
        lines = []
        
        for i in range(self.n):
            row = []
            for j in range(self.n):
                if solution and solution[i][j] == 1:
                    if i == 0 and j == 0:
                        row.append("★")
                    elif i == self.n - 1 and j == self.n - 1:
                        row.append("◎")
                    else:
                        row.append("●")
                elif self.maze[i][j] == 1:
                    row.append("·")
                else:
                    row.append("█")
            lines.append(" ".join(row))
        
        return "\n".join(lines)


# Demo
if __name__ == "__main__":
    print("Rat in a Maze Demo")
    print("=" * 50)
    
    # Example maze
    maze = [
        [1, 0, 0, 0],
        [1, 1, 0, 1],
        [0, 1, 0, 0],
        [1, 1, 1, 1]
    ]
    
    print("\n1. Maze:")
    for row in maze:
        print("  ", row)
    
    # 2-direction solution
    print("\n2. Two-Direction Solution (D/R only):")
    solution = solve_maze_2dir(maze)
    if solution:
        for row in solution:
            print("  ", row)
    else:
        print("   No solution")
    
    # 4-direction solution
    print("\n3. Four-Direction Solution:")
    path = solve_maze_4dir(maze)
    print(f"   Path: {path}")
    
    # All paths
    print("\n4. All Paths (for smaller maze):")
    small_maze = [[1, 1, 1], [1, 1, 1], [1, 1, 1]]
    paths = find_all_paths(small_maze)
    print(f"   Found {len(paths)} paths")
    for p in paths[:5]:
        print(f"   - {p}")
    if len(paths) > 5:
        print(f"   ... and {len(paths) - 5} more")
    
    # BFS shortest path
    print("\n5. BFS Shortest Path:")
    result = shortest_path_bfs(maze)
    if result:
        length, path = result
        print(f"   Length: {length}")
        print(f"   Path: {path}")
    
    # MazeSolver class
    print("\n6. MazeSolver with Visualization:")
    solver = MazeSolver(maze)
    result = solver.solve(method="bfs")
    
    print(f"   Solvable: {result.solvable}")
    print(f"   Path: {result.path}")
    print(f"   Cells explored: {result.cells_explored}")
    print("\n   Visualization:")
    print(solver.visualize(result.solution_matrix))
```

## 8. Applications

### 8.1 Common Use Cases

- **Robot navigation**: Path planning in grid environments
- **Game AI**: NPC movement through obstacles
- **Circuit routing**: PCB trace finding
- **Geographic routing**: GPS navigation
- **Puzzle solving**: Various maze-based puzzles

### 8.2 Related Problems

| Problem | Description |
|---------|-------------|
| Shortest Path | BFS in unweighted graph |
| A* Algorithm | Heuristic-based path finding |
| Flood Fill | Connected component finding |
| Knight's Tour | Chess piece traversal |

## 9. Real-World Software Engineering Applications

### 9.1 Production Example: Game AI Pathfinding

```python
from typing import Dict, List, Optional, Tuple, Set
from dataclasses import dataclass, field
from enum import Enum
from collections import deque
import heapq
import time


class TerrainType(Enum):
    """Terrain types with movement costs."""
    GROUND = 1
    WATER = 3
    FOREST = 2
    MOUNTAIN = 5
    WALL = float('inf')


@dataclass
class Position:
    """2D position in the game world."""
    x: int
    y: int
    
    def __hash__(self):
        return hash((self.x, self.y))
    
    def __eq__(self, other):
        return self.x == other.x and self.y == other.y
    
    def manhattan_distance(self, other: 'Position') -> int:
        return abs(self.x - other.x) + abs(self.y - other.y)


@dataclass
class PathResult:
    """Result of pathfinding."""
    found: bool
    path: List[Position]
    cost: float
    nodes_explored: int
    time_ms: float


class GameMap:
    """
    Game map with terrain-based pathfinding.
    """
    
    def __init__(self, width: int, height: int):
        self.width = width
        self.height = height
        self.terrain: List[List[TerrainType]] = [
            [TerrainType.GROUND] * width for _ in range(height)
        ]
        self.entities: Dict[Position, str] = {}
    
    def set_terrain(self, x: int, y: int, terrain: TerrainType):
        """Set terrain at position."""
        if 0 <= x < self.width and 0 <= y < self.height:
            self.terrain[y][x] = terrain
    
    def get_terrain(self, x: int, y: int) -> TerrainType:
        """Get terrain at position."""
        if 0 <= x < self.width and 0 <= y < self.height:
            return self.terrain[y][x]
        return TerrainType.WALL
    
    def is_walkable(self, x: int, y: int) -> bool:
        """Check if position is walkable."""
        terrain = self.get_terrain(x, y)
        return terrain != TerrainType.WALL
    
    def get_movement_cost(self, x: int, y: int) -> float:
        """Get movement cost at position."""
        return self.get_terrain(x, y).value


class PathFinder:
    """
    A* pathfinding for game AI.
    
    Supports weighted terrain and multiple heuristics.
    """
    
    def __init__(self, game_map: GameMap):
        self.map = game_map
        self.directions = [
            (0, 1), (1, 0), (0, -1), (-1, 0),  # Cardinal
            (1, 1), (1, -1), (-1, 1), (-1, -1)  # Diagonal
        ]
    
    def find_path(
        self,
        start: Position,
        goal: Position,
        allow_diagonal: bool = True
    ) -> PathResult:
        """
        Find optimal path using A*.
        
        Uses Manhattan distance as heuristic.
        """
        start_time = time.time()
        
        if not self.map.is_walkable(start.x, start.y):
            return PathResult(False, [], 0, 0, 0)
        if not self.map.is_walkable(goal.x, goal.y):
            return PathResult(False, [], 0, 0, 0)
        
        # Priority queue: (f_score, counter, position)
        counter = 0
        open_set = [(0, counter, start)]
        came_from: Dict[Position, Position] = {}
        
        g_score: Dict[Position, float] = {start: 0}
        nodes_explored = 0
        
        moves = self.directions if allow_diagonal else self.directions[:4]
        
        while open_set:
            _, _, current = heapq.heappop(open_set)
            nodes_explored += 1
            
            if current == goal:
                path = self._reconstruct_path(came_from, current)
                elapsed = (time.time() - start_time) * 1000
                return PathResult(
                    True, path, g_score[current],
                    nodes_explored, elapsed
                )
            
            for dx, dy in moves:
                neighbor = Position(current.x + dx, current.y + dy)
                
                if not self.map.is_walkable(neighbor.x, neighbor.y):
                    continue
                
                # Diagonal movement costs more
                move_cost = 1.414 if (dx != 0 and dy != 0) else 1.0
                terrain_cost = self.map.get_movement_cost(neighbor.x, neighbor.y)
                
                tentative_g = g_score[current] + move_cost * terrain_cost
                
                if neighbor not in g_score or tentative_g < g_score[neighbor]:
                    came_from[neighbor] = current
                    g_score[neighbor] = tentative_g
                    f_score = tentative_g + neighbor.manhattan_distance(goal)
                    counter += 1
                    heapq.heappush(open_set, (f_score, counter, neighbor))
        
        elapsed = (time.time() - start_time) * 1000
        return PathResult(False, [], 0, nodes_explored, elapsed)
    
    def _reconstruct_path(
        self,
        came_from: Dict[Position, Position],
        current: Position
    ) -> List[Position]:
        """Reconstruct path from A* result."""
        path = [current]
        while current in came_from:
            current = came_from[current]
            path.append(current)
        path.reverse()
        return path
    
    def find_path_with_waypoints(
        self,
        start: Position,
        waypoints: List[Position],
        goal: Position
    ) -> PathResult:
        """
        Find path through multiple waypoints.
        """
        full_path = []
        total_cost = 0
        total_explored = 0
        total_time = 0
        
        current = start
        points = waypoints + [goal]
        
        for target in points:
            result = self.find_path(current, target)
            
            if not result.found:
                return PathResult(
                    False, [], 0, total_explored, total_time
                )
            
            # Append path (skip first if not start)
            if full_path:
                full_path.extend(result.path[1:])
            else:
                full_path.extend(result.path)
            
            total_cost += result.cost
            total_explored += result.nodes_explored
            total_time += result.time_ms
            current = target
        
        return PathResult(True, full_path, total_cost, total_explored, total_time)


class AINavigator:
    """
    Game AI navigation system.
    
    Handles pathfinding, path smoothing, and movement.
    """
    
    def __init__(self, game_map: GameMap):
        self.map = game_map
        self.pathfinder = PathFinder(game_map)
        self.current_path: List[Position] = []
        self.path_index = 0
    
    def navigate_to(
        self,
        start: Position,
        goal: Position,
        smooth: bool = True
    ) -> bool:
        """
        Calculate navigation path.
        """
        result = self.pathfinder.find_path(start, goal)
        
        if not result.found:
            return False
        
        self.current_path = result.path
        if smooth:
            self.current_path = self._smooth_path(self.current_path)
        
        self.path_index = 0
        return True
    
    def get_next_position(self) -> Optional[Position]:
        """Get next position in path."""
        if self.path_index >= len(self.current_path):
            return None
        
        pos = self.current_path[self.path_index]
        self.path_index += 1
        return pos
    
    def _smooth_path(self, path: List[Position]) -> List[Position]:
        """
        Smooth path by removing unnecessary waypoints.
        
        Uses line-of-sight checks.
        """
        if len(path) <= 2:
            return path
        
        smoothed = [path[0]]
        current_idx = 0
        
        while current_idx < len(path) - 1:
            # Find furthest visible point
            furthest = current_idx + 1
            
            for i in range(len(path) - 1, current_idx + 1, -1):
                if self._has_line_of_sight(path[current_idx], path[i]):
                    furthest = i
                    break
            
            smoothed.append(path[furthest])
            current_idx = furthest
        
        return smoothed
    
    def _has_line_of_sight(self, start: Position, end: Position) -> bool:
        """
        Check if there's clear line of sight between positions.
        
        Uses Bresenham's line algorithm.
        """
        x0, y0 = start.x, start.y
        x1, y1 = end.x, end.y
        
        dx = abs(x1 - x0)
        dy = abs(y1 - y0)
        sx = 1 if x0 < x1 else -1
        sy = 1 if y0 < y1 else -1
        err = dx - dy
        
        while True:
            if not self.map.is_walkable(x0, y0):
                return False
            
            if x0 == x1 and y0 == y1:
                break
            
            e2 = 2 * err
            if e2 > -dy:
                err -= dy
                x0 += sx
            if e2 < dx:
                err += dx
                y0 += sy
        
        return True


class FloodFill:
    """
    Flood fill for finding reachable areas.
    """
    
    @staticmethod
    def find_reachable(
        game_map: GameMap,
        start: Position,
        max_cost: float = float('inf')
    ) -> Set[Position]:
        """
        Find all positions reachable from start within cost limit.
        """
        reachable = set()
        queue = deque([(start, 0.0)])
        visited = {start}
        
        while queue:
            pos, cost = queue.popleft()
            
            if cost <= max_cost:
                reachable.add(pos)
            
            for dx, dy in [(0, 1), (1, 0), (0, -1), (-1, 0)]:
                neighbor = Position(pos.x + dx, pos.y + dy)
                
                if neighbor in visited:
                    continue
                
                if not game_map.is_walkable(neighbor.x, neighbor.y):
                    continue
                
                new_cost = cost + game_map.get_movement_cost(neighbor.x, neighbor.y)
                
                if new_cost <= max_cost:
                    visited.add(neighbor)
                    queue.append((neighbor, new_cost))
        
        return reachable


# Demo
if __name__ == "__main__":
    print("Game AI Pathfinding Demo")
    print("=" * 50)
    
    # Create game map
    game_map = GameMap(10, 10)
    
    # Add obstacles
    for i in range(3, 7):
        game_map.set_terrain(i, 3, TerrainType.WALL)
        game_map.set_terrain(i, 6, TerrainType.WALL)
    
    # Add terrain variety
    for i in range(2, 5):
        game_map.set_terrain(i, 4, TerrainType.FOREST)
        game_map.set_terrain(i, 5, TerrainType.WATER)
    
    # Create pathfinder
    navigator = AINavigator(game_map)
    
    start = Position(1, 1)
    goal = Position(8, 8)
    
    print(f"\n1. Finding path from {start} to {goal}")
    
    result = PathFinder(game_map).find_path(start, goal)
    
    print(f"   Path found: {result.found}")
    print(f"   Path cost: {result.cost:.2f}")
    print(f"   Nodes explored: {result.nodes_explored}")
    print(f"   Time: {result.time_ms:.2f}ms")
    
    if result.found:
        print(f"   Path length: {len(result.path)} positions")
    
    # Navigate with smoothing
    print("\n2. Navigation with smoothing:")
    navigator.navigate_to(start, goal, smooth=True)
    print(f"   Smoothed path: {len(navigator.current_path)} positions")
    
    # Find reachable areas
    print("\n3. Flood fill - reachable in 5 moves:")
    reachable = FloodFill.find_reachable(game_map, start, max_cost=5)
    print(f"   Positions reachable: {len(reachable)}")
```

## 10. Comparison

| Algorithm | Time | Optimal Path | Use Case |
|-----------|------|--------------|----------|
| Backtracking | O(2^n²) | No | Small mazes |
| BFS | O(V+E) | Yes (unweighted) | Grid navigation |
| A* | O(V log V) | Yes | Weighted grids |
| Dijkstra | O(V² or V log V) | Yes | General graphs |

## 11. Best Practices

1. **Use BFS** for shortest unweighted path
2. **Mark visited cells** to avoid infinite loops
3. **Prune early** when dead ends detected
4. **Consider heuristics** for large mazes (A*)
5. **Handle edge cases**: empty maze, blocked start/end

## 12. References

- [Wikipedia: Maze Solving Algorithm](https://en.wikipedia.org/wiki/Maze_solving_algorithm)
- [A* Search Algorithm](https://en.wikipedia.org/wiki/A*_search_algorithm)
- Cormen, T. et al. "Introduction to Algorithms" - Graph Algorithms
