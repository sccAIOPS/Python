# Backtracking & Greedy Algorithms

This section contains comprehensive documentation for backtracking and greedy algorithms implemented in TheAlgorithms/Python repository.

## Categories

### Backtracking Algorithms

| Algorithm | Description | Complexity |
|-----------|-------------|------------|
| [N-Queens](n_queens.md) | Place N queens on NxN board | O(N!) |
| [Sudoku Solver](sudoku_solver.md) | Solve 9x9 Sudoku puzzles | O(9^(n²)) |
| [Subset Sum](subset_sum.md) | Find subsets with target sum | O(2^n) |
| [Hamiltonian Cycle](hamiltonian_cycle.md) | Find cycle visiting all vertices | O(N!) |
| [Graph Coloring](graph_coloring.md) | Color graph with minimum colors | O(m^V) |
| [Rat in Maze](rat_in_maze.md) | Find path through maze | O(2^(n²)) |
| [Word Search](word_search.md) | Find words in letter grid | O(N×M×4^L) |

### Greedy Algorithms

| Algorithm | Description | Complexity |
|-----------|-------------|------------|
| [Fractional Knapsack](fractional_knapsack.md) | Maximize value with weight constraint | O(n log n) |
| [Activity Selection](activity_selection.md) | Select maximum non-overlapping activities | O(n log n) |
| [Huffman Coding](huffman_coding.md) | Optimal prefix-free encoding | O(n log n) |
| [Job Scheduling](job_scheduling.md) | Maximize profit with deadlines | O(n²) / O(n log n) |
| [Coin Change (Greedy)](coin_change_greedy.md) | Minimum coins for amount | O(n) |
| [Minimum Spanning Tree](minimum_spanning_tree.md) | Prim's and Kruskal's algorithms | O(E log V) |

## Algorithm Paradigms

### Backtracking Pattern

```
def backtrack(state, choices):
    if is_solution(state):
        record_solution(state)
        return
    
    for choice in choices:
        if is_valid(state, choice):
            make_choice(state, choice)
            backtrack(state, remaining_choices)
            undo_choice(state, choice)  # Backtrack
```

### Greedy Pattern

```
def greedy(items):
    result = []
    
    # Sort by greedy criterion
    items = sort_by_criterion(items)
    
    for item in items:
        if is_feasible(result, item):
            result.append(item)
    
    return result
```

## When to Use

### Backtracking

| Scenario | Example |
|----------|---------|
| Find all solutions | All permutations, combinations |
| Constraint satisfaction | Sudoku, N-Queens |
| Optimization with constraints | Subset sum, Knapsack |
| Path finding | Maze solving, Knight's tour |
| Game solving | Chess puzzles, Crosswords |

### Greedy

| Scenario | Example |
|----------|---------|
| Optimal substructure | Fractional knapsack |
| Greedy choice property | Activity selection |
| Huffman-like problems | Data compression |
| Interval scheduling | Meeting rooms |
| Graph algorithms | MST, Shortest path (Dijkstra) |

## Complexity Comparison

| Algorithm | Time | Space | Optimal? |
|-----------|------|-------|----------|
| Backtracking | Exponential | O(n) - O(n²) | Yes (finds all) |
| Greedy | Polynomial | O(1) - O(n) | Not always |
| Dynamic Programming | Polynomial | O(n) - O(n²) | Yes |
| Brute Force | Exponential | O(1) | Yes |

## Key Differences

### Backtracking vs Greedy

| Aspect | Backtracking | Greedy |
|--------|--------------|--------|
| Approach | Explore all paths | Make locally optimal choice |
| Reversal | Can undo choices | No reversal |
| Solutions | Finds all/optimal | May find suboptimal |
| Speed | Slower | Faster |
| Memory | Call stack | Minimal |

### Backtracking vs Dynamic Programming

| Aspect | Backtracking | DP |
|--------|--------------|-----|
| Subproblems | No overlap assumed | Overlapping subproblems |
| Approach | Top-down DFS | Bottom-up/Memoization |
| All solutions | Yes | Usually one optimal |
| Pruning | Constraint-based | Value-based |

## Directory Structure

```
09_backtracking_greedy/
├── index.md                    # This file
├── n_queens.md                 # N-Queens problem
├── sudoku_solver.md            # Sudoku solver
├── subset_sum.md               # Subset sum problem
├── hamiltonian_cycle.md        # Hamiltonian cycle
├── graph_coloring.md           # Graph coloring
├── rat_in_maze.md              # Rat in maze
├── word_search.md              # Word search in grid
├── fractional_knapsack.md      # Fractional knapsack
├── activity_selection.md       # Activity selection
├── huffman_coding.md           # Huffman coding
├── job_scheduling.md           # Job scheduling
├── coin_change_greedy.md       # Greedy coin change
└── minimum_spanning_tree.md    # MST (Prim's, Kruskal's)
```

## See Also

- [Dynamic Programming](../04_dynamic_programming/) - Optimization with subproblems
- [Graph Algorithms](../03_graphs/) - Graph traversal and algorithms
- [Sorting Algorithms](../01_sorting/) - Prerequisite for many greedy algorithms
