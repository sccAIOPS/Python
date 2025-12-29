# Graph Algorithms

This directory contains comprehensive documentation for graph algorithms implemented in the TheAlgorithms/Python repository.

## Overview

Graph algorithms are essential for solving problems involving relationships between entities. They cover traversal, shortest paths, minimum spanning trees, connectivity analysis, and network flow.

## Algorithm Categories

### Graph Traversal
| Algorithm | Time Complexity | Space | Best For |
|-----------|----------------|-------|----------|
| [Breadth-First Search (BFS)](breadth_first_search.md) | O(V+E) | O(V) | Shortest path (unweighted) |
| [Depth-First Search (DFS)](depth_first_search.md) | O(V+E) | O(V) | Cycle detection, connectivity |
| [Bidirectional BFS](bidirectional_bfs.md) | O(b^(d/2)) | O(b^(d/2)) | Faster path finding |
| [Bidirectional Search](bidirectional_search.md) | O(b^(d/2)) | O(b^(d/2)) | BFS/Dijkstra from both ends |

### Graph Representations
| Representation | Space | Edge Query | Best For |
|----------------|-------|------------|----------|
| [Graph Representations](graph_representations.md) | O(V+E) or O(V²) | O(1) or O(deg) | Various use cases |

### Shortest Path Algorithms
| Algorithm | Time Complexity | Space | Best For |
|-----------|----------------|-------|----------|
| [Dijkstra's Algorithm](dijkstra.md) | O((V+E)log V) | O(V) | Non-negative weights |
| [Bellman-Ford](bellman_ford.md) | O(VE) | O(V) | Negative weights |
| [Floyd-Warshall](floyd_warshall.md) | O(V³) | O(V²) | All pairs shortest path |
| [A* Search](a_star.md) | O(E) | O(V) | Heuristic-guided search |
| [Bidirectional A*](bidirectional_a_star.md) | O(b^(d/2)) | O(b^(d/2)) | Optimized A* |
| [Greedy Best-First](greedy_best_first.md) | O(E) | O(V) | Fast approximation |

### Minimum Spanning Tree
| Algorithm | Time Complexity | Space | Best For |
|-----------|----------------|-------|----------|
| [Kruskal's Algorithm](kruskal.md) | O(E log E) | O(V) | Sparse graphs |
| [Prim's Algorithm](prim.md) | O(E log V) | O(V) | Dense graphs |
| [Boruvka's Algorithm](boruvka.md) | O(E log V) | O(V) | Parallel MST |

### Graph Connectivity
| Algorithm | Time Complexity | Space | Best For |
|-----------|----------------|-------|----------|
| [Connected Components](connected_components.md) | O(V+E) | O(V) | Undirected graphs |
| [Strongly Connected Components](strongly_connected_components.md) | O(V+E) | O(V) | Directed graphs |
| [Tarjan's SCC](tarjans_scc.md) | O(V+E) | O(V) | Single pass SCC |
| [Kosaraju's SCC](kosaraju_scc.md) | O(V+E) | O(V) | Two-pass SCC |
| [Articulation Points](articulation_points.md) | O(V+E) | O(V) | Critical nodes |
| [Finding Bridges](finding_bridges.md) | O(V+E) | O(V) | Critical edges |

### Topological Sorting
| Algorithm | Time Complexity | Space | Best For |
|-----------|----------------|-------|----------|
| [Kahn's Algorithm](kahns_algorithm.md) | O(V+E) | O(V) | BFS-based |
| [DFS Topological Sort](topological_sort.md) | O(V+E) | O(V) | DFS-based |

### Network Flow
| Algorithm | Time Complexity | Space | Best For |
|-----------|----------------|-------|----------|
| [Dinic's Algorithm](dinic.md) | O(V²E) | O(V+E) | Maximum flow |
| [Edmonds-Karp](edmonds_karp.md) | O(VE²) | O(V+E) | Max flow (BFS) |

### Graph Matching
| Algorithm | Time Complexity | Space | Best For |
|-----------|----------------|-------|----------|
| [Gale-Shapley](gale_shapley.md) | O(n²) | O(n) | Stable matching |
| [Min Vertex Cover](min_vertex_cover.md) | O(VE) | O(V) | Covering problem |

### Special Algorithms
| Algorithm | Time Complexity | Space | Best For |
|-----------|----------------|-------|----------|
| [PageRank](pagerank.md) | O(iterations × E) | O(V) | Web ranking |
| [Markov Chain](markov_chain.md) | O(n²) | O(n²) | Stochastic modeling |
| [Karger's Min-Cut](karger.md) | O(V²) | O(V²) | Min cut (randomized) |
| [Ant Colony Optimization](ant_colony_optimization.md) | O(iterations × ants × E) | O(E) | TSP heuristic |
| [Eulerian Path/Circuit](eulerian_path.md) | O(E) | O(E) | Path visiting all edges |

### Graph Mining & Analysis
| Algorithm | Time Complexity | Space | Best For |
|-----------|----------------|-------|----------|
| [Frequent Pattern Graph Miner](frequent_pattern_graph_miner.md) | O(n × k × \|S\|) | O(\|E\| + \|V\|) | Subgraph pattern mining |
| [Lanczos Eigenvectors](lanczos_eigenvectors.md) | O(k × n) | O(k × n) | Spectral clustering |
| [Random Graph Generator](random_graph_generator.md) | O(n²) | O(n + m) | Graph generation |
| [Minimum Vertex Cover](minimum_vertex_cover.md) | O(VE) | O(V) | Covering problem |
| [Minimum Path Sum](minimum_path_sum.md) | O(m×n) | O(n) | Grid path optimization |

## Comparison Matrix

### Shortest Path Algorithm Selection

```
Need shortest path?
├── Single source?
│   ├── Negative weights?
│   │   ├── Yes → Bellman-Ford
│   │   └── No → Dijkstra
│   └── With heuristic?
│       ├── Yes → A*
│       └── No → Dijkstra
└── All pairs?
    └── Floyd-Warshall
```

### MST Algorithm Selection

```
Find MST?
├── Sparse graph (E ≈ V)?
│   └── Kruskal's Algorithm
├── Dense graph (E ≈ V²)?
│   └── Prim's Algorithm
└── Parallel computing?
    └── Boruvka's Algorithm
```

## Graph Representations

### Adjacency List
```python
# Memory: O(V + E)
graph = {
    'A': [('B', 5), ('C', 3)],
    'B': [('D', 2)],
    'C': [('D', 1)],
    'D': []
}
```

### Adjacency Matrix
```python
# Memory: O(V²)
matrix = [
    [0, 5, 3, 0],  # A
    [0, 0, 0, 2],  # B
    [0, 0, 0, 1],  # C
    [0, 0, 0, 0]   # D
]
```

## Learning Path

### Beginner
1. [Breadth-First Search](breadth_first_search.md) - Foundation traversal
2. [Depth-First Search](depth_first_search.md) - Recursive exploration
3. [Dijkstra's Algorithm](dijkstra.md) - Shortest paths

### Intermediate
4. [Bellman-Ford](bellman_ford.md) - Negative weight handling
5. [Kruskal's Algorithm](kruskal.md) - Minimum spanning tree
6. [Topological Sort](topological_sort.md) - DAG ordering
7. [Connected Components](connected_components.md) - Graph connectivity

### Advanced
8. [A* Search](a_star.md) - Heuristic-guided pathfinding
9. [Tarjan's SCC](tarjans_scc.md) - Strongly connected components
10. [Dinic's Algorithm](dinic.md) - Maximum flow
11. [PageRank](page_rank.md) - Web ranking algorithm

## See Also

- [Searching Algorithms](../02_searching/index.md) - Basic search techniques
- [Data Structures](../06_data_structures/index.md) - Graph representations
- [Dynamic Programming](../05_dynamic_programming/index.md) - DP on graphs
