# Hamiltonian Cycle

## Overview
- **Category**: Backtracking / Graph Theory
- **Complexity**: Time: O(n!) worst case | Space: O(n)
- **Type**: NP-Complete problem
- **Source Files**: [backtracking/hamiltonian_cycle.py](../../../backtracking/hamiltonian_cycle.py)

## 1. Mathematical Foundation

### 1.1 Problem Definition

**Hamiltonian Path**: A path that visits every vertex exactly once.

**Hamiltonian Cycle**: A Hamiltonian path that returns to the starting vertex.

Given graph $G = (V, E)$ with $n = |V|$ vertices:
- Find cycle $(v_1, v_2, ..., v_n, v_1)$ where:
  - All $v_i$ are distinct
  - $(v_i, v_{i+1}) \in E$ for all $i$
  - $(v_n, v_1) \in E$

### 1.2 Necessary Conditions

For a Hamiltonian cycle to exist:
- Graph must be connected
- $\deg(v) \geq 2$ for all vertices $v$

### 1.3 Sufficient Conditions

**Dirac's Theorem**: If $n \geq 3$ and $\deg(v) \geq n/2$ for all $v$, then $G$ has a Hamiltonian cycle.

**Ore's Theorem**: If $n \geq 3$ and $\deg(u) + \deg(v) \geq n$ for all non-adjacent pairs $u, v$, then $G$ has a Hamiltonian cycle.

### 1.4 Complexity

- **Decision problem**: NP-Complete
- **Counting**: #P-Complete
- No known polynomial algorithm for general graphs

### 1.5 Special Cases

| Graph Type | Complexity |
|------------|------------|
| Complete graph $K_n$ | Always has $(n-1)!/2$ cycles |
| Cycle graph $C_n$ | Has exactly 2 cycles |
| Bipartite $K_{m,n}$ | Has cycle iff $m = n$ |
| Grid graphs | Polynomial time solvable |

### 1.6 Relation to TSP

Traveling Salesman Problem (TSP) = Finding **minimum weight** Hamiltonian cycle.

## 2. Pruning Strategies

### 2.1 Degree Pruning
Skip vertices with degree < 2 (dead ends).

### 2.2 Warnsdorff's Heuristic
For each choice, prefer the vertex with fewest unvisited neighbors (most constrained first).

### 2.3 Neighbor Look-Ahead
If remaining unvisited vertices form disconnected components, backtrack.

### 2.4 Articulation Points
Avoid visiting articulation points until necessary (they disconnect the graph).

## 3. Pseudocode

### 3.1 Basic Backtracking

```
ALGORITHM HamiltonianCycle(graph)
    INPUT: Adjacency list/matrix representation of graph
    OUTPUT: Hamiltonian cycle or None if not exists
    
    n ← number of vertices
    path ← [0]  // Start from vertex 0
    visited ← set containing 0
    
    FUNCTION Backtrack(current)
        // Check if cycle is complete
        if |path| = n then
            // Check if we can return to start
            if graph.hasEdge(current, 0) then
                return path + [0]  // Complete cycle
            else
                return None
        
        // Try each unvisited neighbor
        for each neighbor of current do
            if neighbor not in visited then
                visited.add(neighbor)
                path.append(neighbor)
                
                result ← Backtrack(neighbor)
                if result ≠ None then
                    return result
                
                // Backtrack
                visited.remove(neighbor)
                path.pop()
        
        return None
    
    return Backtrack(0)
```

### 3.2 With Warnsdorff's Heuristic

```
ALGORITHM HamiltonianCycleWarnsdorff(graph)
    INPUT: Graph representation
    OUTPUT: Hamiltonian cycle or None
    
    n ← number of vertices
    path ← [0]
    visited ← {0}
    
    FUNCTION CountUnvisited(vertex)
        count ← 0
        for each neighbor of vertex do
            if neighbor not in visited then
                count ← count + 1
        return count
    
    FUNCTION Backtrack(current)
        if |path| = n then
            if graph.hasEdge(current, 0) then
                return path + [0]
            return None
        
        // Sort neighbors by number of unvisited neighbors (ascending)
        candidates ← [(CountUnvisited(n), n) for n in neighbors(current) if n not in visited]
        Sort candidates by first element (ascending)
        
        for (_, neighbor) in candidates do
            visited.add(neighbor)
            path.append(neighbor)
            
            result ← Backtrack(neighbor)
            if result ≠ None then
                return result
            
            visited.remove(neighbor)
            path.pop()
        
        return None
    
    return Backtrack(0)
```

### 3.3 Finding All Cycles

```
ALGORITHM AllHamiltonianCycles(graph)
    INPUT: Graph representation
    OUTPUT: List of all Hamiltonian cycles
    
    cycles ← []
    n ← number of vertices
    
    FUNCTION Backtrack(path, visited)
        current ← path[-1]
        
        if |path| = n then
            if graph.hasEdge(current, path[0]) then
                // Normalize to avoid duplicates
                normalized ← normalize(path)
                if normalized not in cycles then
                    cycles.append(normalized)
            return
        
        for each neighbor of current do
            if neighbor not in visited then
                visited.add(neighbor)
                path.append(neighbor)
                Backtrack(path, visited)
                visited.remove(neighbor)
                path.pop()
    
    Backtrack([0], {0})
    return cycles
```

## 4. Step-by-Step Example

### Example: Pentagon Graph

```
Graph:
    0 ─── 1
    |╲   ╱|
    | ╲ ╱ |
    |  X  |
    | ╱ ╲ |
    |╱   ╲|
    4 ─── 3 ─── 2
    
Adjacency:
0: [1, 3, 4]
1: [0, 2, 4]
2: [1, 3]
3: [0, 2, 4]
4: [0, 1, 3]
```

**Backtracking Trace:**

```
Start: path=[0], visited={0}

Try neighbor 1:
  path=[0,1], visited={0,1}
  
  Try neighbor 2:
    path=[0,1,2], visited={0,1,2}
    
    Try neighbor 3:
      path=[0,1,2,3], visited={0,1,2,3}
      
      Try neighbor 4:
        path=[0,1,2,3,4], visited={0,1,2,3,4}
        All vertices visited!
        Check edge (4,0): YES ✓
        
        Return: [0,1,2,3,4,0]
```

**Solution: 0 → 1 → 2 → 3 → 4 → 0**

### Visual Solution

```
    0 ─→─ 1
    ↑     │
    │     ↓
    │     2
    │     │
    │     ↓
    4 ←── 3
```

## 5. Complexity Analysis

| Aspect | Complexity | Notes |
|--------|------------|-------|
| Time (worst) | O(n!) | Try all permutations |
| Time (best) | O(n) | First path works |
| Time (average) | O(b^n) | b = branching factor |
| Space | O(n) | Path and visited set |

### With Pruning

| Optimization | Expected Speedup |
|--------------|------------------|
| Warnsdorff | Significant for sparse graphs |
| Degree check | Eliminates dead ends early |
| Connected check | Prunes disconnected subproblems |

## 6. Visual Representation

### 6.1 Search Tree

```
                         [0]
                       ╱  │  ╲
                     1    3    4
                   ╱ ╲   ╱╲   ╱╲
                  2   4 2  4 1  3
                  │   │ │  │ │  │
                  3   × 4  × 2  2
                  │     │      │
                  4     ×      4
                  │            │
                 (0)          (0)
                  ✓            ✓
                  
× = dead end (no edge to next or back to start)
✓ = valid Hamiltonian cycle found
```

### 6.2 Graph Traversal

```
Step 1: Start at 0     Step 2: Go to 1      Step 3: Go to 2
    (0)─── 1               0 ─(1)              0 ─── 1
    |╲   ╱|              ╱     ╲             ╱     ╲
    4 ─── 3 ─── 2       4       2           4      (2)
    
Step 4: Go to 3        Step 5: Go to 4      Step 6: Return to 0
    0 ─── 1               0 ─── 1              (0)─── 1
   ╱     ╲              ╱     ╲               ↑     ╲
  4      2             (4)     2              4       2
   ╲   ╱                ╲   ╱                 ╲     ╱
    (3)                   3                     3
```

## 7. Implementation

```python
from typing import List, Optional, Set, Tuple
from collections import defaultdict


class Graph:
    """
    Graph representation for Hamiltonian cycle algorithms.
    """
    
    def __init__(self, vertices: int):
        """
        Initialize graph with given number of vertices.
        
        >>> g = Graph(5)
        >>> g.vertices
        5
        """
        self.vertices = vertices
        self.adj: List[Set[int]] = [set() for _ in range(vertices)]
    
    def add_edge(self, u: int, v: int):
        """
        Add undirected edge.
        
        >>> g = Graph(3)
        >>> g.add_edge(0, 1)
        >>> 1 in g.adj[0] and 0 in g.adj[1]
        True
        """
        self.adj[u].add(v)
        self.adj[v].add(u)
    
    def has_edge(self, u: int, v: int) -> bool:
        """Check if edge exists."""
        return v in self.adj[u]
    
    def degree(self, v: int) -> int:
        """Get degree of vertex."""
        return len(self.adj[v])
    
    def neighbors(self, v: int) -> Set[int]:
        """Get neighbors of vertex."""
        return self.adj[v]


def hamiltonian_cycle(graph: Graph) -> Optional[List[int]]:
    """
    Find a Hamiltonian cycle using backtracking.
    
    >>> g = Graph(5)
    >>> edges = [(0,1), (1,2), (2,3), (3,4), (4,0), (0,3), (1,4)]
    >>> for u, v in edges:
    ...     g.add_edge(u, v)
    >>> cycle = hamiltonian_cycle(g)
    >>> cycle is not None and len(cycle) == 6
    True
    >>> cycle[0] == cycle[-1]  # Returns to start
    True
    """
    n = graph.vertices
    
    if n == 0:
        return None
    if n == 1:
        return [0, 0] if graph.has_edge(0, 0) else None
    
    # Check necessary condition
    for v in range(n):
        if graph.degree(v) < 2:
            return None
    
    path = [0]
    visited = {0}
    
    def backtrack(current: int) -> bool:
        if len(path) == n:
            # Check if we can return to start
            return graph.has_edge(current, 0)
        
        for neighbor in graph.neighbors(current):
            if neighbor not in visited:
                visited.add(neighbor)
                path.append(neighbor)
                
                if backtrack(neighbor):
                    return True
                
                visited.remove(neighbor)
                path.pop()
        
        return False
    
    if backtrack(0):
        return path + [0]
    return None


def hamiltonian_cycle_warnsdorff(graph: Graph) -> Optional[List[int]]:
    """
    Find Hamiltonian cycle using Warnsdorff's heuristic.
    
    Prefers vertices with fewer unvisited neighbors.
    Often faster for sparse graphs.
    
    >>> g = Graph(5)
    >>> for i in range(5):
    ...     g.add_edge(i, (i+1) % 5)
    >>> cycle = hamiltonian_cycle_warnsdorff(g)
    >>> cycle is not None
    True
    """
    n = graph.vertices
    
    if n == 0:
        return None
    if n == 1:
        return [0, 0] if graph.has_edge(0, 0) else None
    
    path = [0]
    visited = {0}
    
    def count_unvisited(vertex: int) -> int:
        return sum(1 for n in graph.neighbors(vertex) if n not in visited)
    
    def backtrack(current: int) -> bool:
        if len(path) == n:
            return graph.has_edge(current, 0)
        
        # Get unvisited neighbors sorted by degree (Warnsdorff)
        candidates = [
            (count_unvisited(neighbor), neighbor)
            for neighbor in graph.neighbors(current)
            if neighbor not in visited
        ]
        candidates.sort()  # Prefer vertices with fewer options
        
        for _, neighbor in candidates:
            visited.add(neighbor)
            path.append(neighbor)
            
            if backtrack(neighbor):
                return True
            
            visited.remove(neighbor)
            path.pop()
        
        return False
    
    if backtrack(0):
        return path + [0]
    return None


def hamiltonian_path(graph: Graph, start: int = 0) -> Optional[List[int]]:
    """
    Find a Hamiltonian path (not necessarily a cycle).
    
    >>> g = Graph(4)
    >>> g.add_edge(0, 1)
    >>> g.add_edge(1, 2)
    >>> g.add_edge(2, 3)
    >>> path = hamiltonian_path(g)
    >>> path is not None and len(path) == 4
    True
    """
    n = graph.vertices
    
    if n == 0:
        return None
    if n == 1:
        return [start]
    
    path = [start]
    visited = {start}
    
    def backtrack(current: int) -> bool:
        if len(path) == n:
            return True
        
        for neighbor in graph.neighbors(current):
            if neighbor not in visited:
                visited.add(neighbor)
                path.append(neighbor)
                
                if backtrack(neighbor):
                    return True
                
                visited.remove(neighbor)
                path.pop()
        
        return False
    
    if backtrack(start):
        return path
    return None


def all_hamiltonian_cycles(graph: Graph) -> List[List[int]]:
    """
    Find all unique Hamiltonian cycles.
    
    Cycles are normalized to avoid duplicates 
    (different starting points, directions).
    
    >>> g = Graph(4)
    >>> for i in range(4):
    ...     g.add_edge(i, (i+1) % 4)
    >>> cycles = all_hamiltonian_cycles(g)
    >>> len(cycles)  # Square has 1 unique cycle (ignoring direction)
    1
    """
    n = graph.vertices
    
    if n < 3:
        return []
    
    all_cycles: Set[Tuple[int, ...]] = set()
    path = [0]
    visited = {0}
    
    def normalize_cycle(cycle: List[int]) -> Tuple[int, ...]:
        """Normalize cycle to canonical form."""
        # Remove last element (same as first)
        cycle = cycle[:-1]
        
        # Find minimum element and its index
        min_idx = cycle.index(min(cycle))
        
        # Rotate to start with minimum
        rotated = cycle[min_idx:] + cycle[:min_idx]
        
        # Choose direction that gives lexicographically smaller
        reversed_rotated = [rotated[0]] + rotated[1:][::-1]
        
        return tuple(min(rotated, reversed_rotated))
    
    def backtrack(current: int):
        if len(path) == n:
            if graph.has_edge(current, 0):
                cycle = path + [0]
                normalized = normalize_cycle(cycle)
                all_cycles.add(normalized)
            return
        
        for neighbor in graph.neighbors(current):
            if neighbor not in visited:
                visited.add(neighbor)
                path.append(neighbor)
                
                backtrack(neighbor)
                
                visited.remove(neighbor)
                path.pop()
    
    backtrack(0)
    
    return [list(c) + [c[0]] for c in all_cycles]


def count_hamiltonian_cycles(graph: Graph) -> int:
    """
    Count the number of Hamiltonian cycles.
    
    >>> g = Graph(4)
    >>> # Complete graph K4
    >>> for i in range(4):
    ...     for j in range(i+1, 4):
    ...         g.add_edge(i, j)
    >>> count_hamiltonian_cycles(g)
    3
    """
    return len(all_hamiltonian_cycles(graph))


class HamiltonianSolver:
    """
    Advanced Hamiltonian cycle solver with optimizations.
    """
    
    def __init__(self, graph: Graph):
        self.graph = graph
        self.n = graph.vertices
    
    def has_hamiltonian_cycle(self) -> bool:
        """
        Quick check if Hamiltonian cycle might exist.
        Uses necessary conditions.
        """
        # Must have at least 3 vertices
        if self.n < 3:
            return False
        
        # All vertices must have degree >= 2
        for v in range(self.n):
            if self.graph.degree(v) < 2:
                return False
        
        # Try to find cycle
        return hamiltonian_cycle(self.graph) is not None
    
    def satisfies_dirac(self) -> bool:
        """Check if graph satisfies Dirac's theorem."""
        threshold = self.n / 2
        return all(
            self.graph.degree(v) >= threshold
            for v in range(self.n)
        )
    
    def satisfies_ore(self) -> bool:
        """Check if graph satisfies Ore's theorem."""
        for u in range(self.n):
            for v in range(u + 1, self.n):
                if not self.graph.has_edge(u, v):
                    if self.graph.degree(u) + self.graph.degree(v) < self.n:
                        return False
        return True
    
    def find_cycle(self) -> Optional[List[int]]:
        """Find Hamiltonian cycle with best available method."""
        if self.n < 3:
            return None
        
        # Use Warnsdorff for sparse graphs
        if sum(self.graph.degree(v) for v in range(self.n)) < self.n * self.n / 2:
            result = hamiltonian_cycle_warnsdorff(self.graph)
            if result:
                return result
        
        return hamiltonian_cycle(self.graph)
```

## 8. Applications

### 8.1 Common Use Cases

- **Traveling Salesman Problem**: Find shortest tour
- **Logistics and routing**: Delivery route planning
- **DNA sequencing**: Finding overlapping sequences
- **Circuit design**: Minimizing connections
- **Graph analysis**: Testing graph properties

### 8.2 Related Problems

| Problem | Description |
|---------|-------------|
| Hamiltonian Path | Visit all vertices (no return) |
| Eulerian Cycle | Visit all edges exactly once |
| TSP | Shortest Hamiltonian cycle |
| Knight's Tour | Hamiltonian path on chessboard |

## 9. Real-World Software Engineering Applications

### 9.1 Production Example: Delivery Route Optimizer

```python
from typing import Dict, List, Optional, Tuple, Set
from dataclasses import dataclass
from enum import Enum
import math
import heapq


class VehicleType(Enum):
    BICYCLE = "bicycle"
    MOTORCYCLE = "motorcycle"
    VAN = "van"
    TRUCK = "truck"


@dataclass
class Location:
    """Delivery location."""
    id: str
    name: str
    latitude: float
    longitude: float
    priority: int = 1
    time_window: Optional[Tuple[int, int]] = None  # (start_hour, end_hour)
    service_time: int = 5  # minutes


@dataclass
class DeliveryRoute:
    """Result of route optimization."""
    locations: List[Location]
    total_distance: float
    estimated_time: int  # minutes
    is_feasible: bool
    warnings: List[str]


class DeliveryRouteOptimizer:
    """
    Optimize delivery routes using Hamiltonian cycle concepts.
    
    Finds efficient routes visiting all delivery points.
    """
    
    def __init__(
        self,
        depot: Location,
        vehicle_type: VehicleType = VehicleType.VAN
    ):
        self.depot = depot
        self.vehicle_type = vehicle_type
        self.speed = self._get_vehicle_speed()
    
    def _get_vehicle_speed(self) -> float:
        """Get average speed in km/h for vehicle type."""
        speeds = {
            VehicleType.BICYCLE: 15,
            VehicleType.MOTORCYCLE: 40,
            VehicleType.VAN: 30,
            VehicleType.TRUCK: 25
        }
        return speeds[self.vehicle_type]
    
    def _haversine_distance(
        self,
        loc1: Location,
        loc2: Location
    ) -> float:
        """Calculate distance between two locations in km."""
        R = 6371  # Earth's radius in km
        
        lat1, lon1 = math.radians(loc1.latitude), math.radians(loc1.longitude)
        lat2, lon2 = math.radians(loc2.latitude), math.radians(loc2.longitude)
        
        dlat = lat2 - lat1
        dlon = lon2 - lon1
        
        a = (math.sin(dlat/2)**2 + 
             math.cos(lat1) * math.cos(lat2) * math.sin(dlon/2)**2)
        c = 2 * math.asin(math.sqrt(a))
        
        return R * c
    
    def _build_graph(
        self,
        locations: List[Location]
    ) -> Tuple[List[Location], List[List[float]]]:
        """Build distance matrix."""
        all_locs = [self.depot] + locations
        n = len(all_locs)
        
        dist = [[0.0] * n for _ in range(n)]
        
        for i in range(n):
            for j in range(i + 1, n):
                d = self._haversine_distance(all_locs[i], all_locs[j])
                dist[i][j] = d
                dist[j][i] = d
        
        return all_locs, dist
    
    def optimize_route(
        self,
        locations: List[Location]
    ) -> DeliveryRoute:
        """
        Find optimal delivery route visiting all locations.
        
        Uses nearest neighbor heuristic with 2-opt improvement.
        """
        if not locations:
            return DeliveryRoute(
                locations=[self.depot],
                total_distance=0,
                estimated_time=0,
                is_feasible=True,
                warnings=[]
            )
        
        all_locs, dist = self._build_graph(locations)
        n = len(all_locs)
        
        # Nearest neighbor heuristic
        route = self._nearest_neighbor(n, dist)
        
        # 2-opt improvement
        route = self._two_opt(route, dist)
        
        # Calculate metrics
        total_dist = self._route_distance(route, dist)
        travel_time = (total_dist / self.speed) * 60  # minutes
        service_time = sum(all_locs[i].service_time for i in route[1:-1])
        total_time = int(travel_time + service_time)
        
        # Check feasibility
        warnings = []
        is_feasible = True
        
        for i, idx in enumerate(route[1:-1]):
            loc = all_locs[idx]
            if loc.time_window:
                arrival = int(travel_time * (i / (n - 2)))
                if arrival < loc.time_window[0] * 60:
                    warnings.append(f"Early arrival at {loc.name}")
                elif arrival > loc.time_window[1] * 60:
                    warnings.append(f"Late arrival at {loc.name}")
                    is_feasible = False
        
        return DeliveryRoute(
            locations=[all_locs[i] for i in route],
            total_distance=total_dist,
            estimated_time=total_time,
            is_feasible=is_feasible,
            warnings=warnings
        )
    
    def _nearest_neighbor(
        self,
        n: int,
        dist: List[List[float]]
    ) -> List[int]:
        """Nearest neighbor heuristic for TSP."""
        visited = {0}
        route = [0]
        current = 0
        
        while len(visited) < n:
            nearest = -1
            nearest_dist = float('inf')
            
            for j in range(n):
                if j not in visited and dist[current][j] < nearest_dist:
                    nearest = j
                    nearest_dist = dist[current][j]
            
            if nearest == -1:
                break
            
            visited.add(nearest)
            route.append(nearest)
            current = nearest
        
        route.append(0)  # Return to depot
        return route
    
    def _two_opt(
        self,
        route: List[int],
        dist: List[List[float]]
    ) -> List[int]:
        """2-opt improvement for TSP route."""
        improved = True
        
        while improved:
            improved = False
            
            for i in range(1, len(route) - 2):
                for j in range(i + 1, len(route) - 1):
                    # Calculate improvement
                    delta = (
                        dist[route[i-1]][route[j]] +
                        dist[route[i]][route[j+1]] -
                        dist[route[i-1]][route[i]] -
                        dist[route[j]][route[j+1]]
                    )
                    
                    if delta < -0.0001:  # Improvement found
                        route[i:j+1] = route[i:j+1][::-1]
                        improved = True
        
        return route
    
    def _route_distance(
        self,
        route: List[int],
        dist: List[List[float]]
    ) -> float:
        """Calculate total route distance."""
        return sum(
            dist[route[i]][route[i+1]]
            for i in range(len(route) - 1)
        )
    
    def optimize_multiple_vehicles(
        self,
        locations: List[Location],
        num_vehicles: int
    ) -> List[DeliveryRoute]:
        """
        Split deliveries among multiple vehicles.
        
        Uses cluster-first, route-second approach.
        """
        if not locations:
            return []
        
        # Cluster locations
        clusters = self._cluster_locations(locations, num_vehicles)
        
        # Optimize each cluster
        routes = []
        for cluster in clusters:
            route = self.optimize_route(cluster)
            routes.append(route)
        
        return routes
    
    def _cluster_locations(
        self,
        locations: List[Location],
        k: int
    ) -> List[List[Location]]:
        """Simple geographic clustering."""
        if len(locations) <= k:
            return [[loc] for loc in locations]
        
        # K-means-like clustering based on position
        clusters: List[List[Location]] = [[] for _ in range(k)]
        
        # Initialize centroids
        sorted_by_angle = sorted(
            locations,
            key=lambda loc: math.atan2(
                loc.latitude - self.depot.latitude,
                loc.longitude - self.depot.longitude
            )
        )
        
        # Assign to clusters by angle sectors
        for i, loc in enumerate(sorted_by_angle):
            cluster_idx = i * k // len(sorted_by_angle)
            clusters[cluster_idx].append(loc)
        
        return [c for c in clusters if c]


# Demo
if __name__ == "__main__":
    print("Hamiltonian Cycle Demo")
    print("=" * 50)
    
    # Basic example
    print("\n1. Basic Hamiltonian Cycle:")
    g = Graph(5)
    edges = [(0,1), (1,2), (2,3), (3,4), (4,0), (0,3), (1,4)]
    for u, v in edges:
        g.add_edge(u, v)
    
    cycle = hamiltonian_cycle(g)
    print(f"   Vertices: 5")
    print(f"   Edges: {edges}")
    print(f"   Hamiltonian cycle: {cycle}")
    
    # Pentagon
    print("\n2. Cycle Graph (Pentagon):")
    g2 = Graph(5)
    for i in range(5):
        g2.add_edge(i, (i+1) % 5)
    
    cycle = hamiltonian_cycle(g2)
    print(f"   Hamiltonian cycle: {cycle}")
    
    # Complete graph
    print("\n3. Complete Graph K4:")
    g3 = Graph(4)
    for i in range(4):
        for j in range(i+1, 4):
            g3.add_edge(i, j)
    
    cycles = all_hamiltonian_cycles(g3)
    print(f"   Number of unique cycles: {len(cycles)}")
    print(f"   Cycles: {cycles}")
    
    # No Hamiltonian cycle
    print("\n4. Graph without Hamiltonian Cycle:")
    g4 = Graph(4)
    g4.add_edge(0, 1)
    g4.add_edge(1, 2)
    g4.add_edge(2, 3)  # Path, not cycle
    
    cycle = hamiltonian_cycle(g4)
    print(f"   Path graph (0-1-2-3)")
    print(f"   Hamiltonian cycle: {cycle}")
    path = hamiltonian_path(g4)
    print(f"   Hamiltonian path: {path}")
    
    # Delivery optimizer
    print("\n5. Delivery Route Optimizer:")
    depot = Location("DEPOT", "Warehouse", 40.7128, -74.0060)
    
    locations = [
        Location("L1", "Customer A", 40.7589, -73.9851),
        Location("L2", "Customer B", 40.7484, -73.9857),
        Location("L3", "Customer C", 40.7614, -73.9776),
        Location("L4", "Customer D", 40.7527, -73.9772),
    ]
    
    optimizer = DeliveryRouteOptimizer(depot, VehicleType.VAN)
    route = optimizer.optimize_route(locations)
    
    print(f"   Depot: {depot.name}")
    print(f"   Deliveries: {len(locations)}")
    print(f"   Route: {' → '.join(loc.name for loc in route.locations)}")
    print(f"   Total distance: {route.total_distance:.2f} km")
    print(f"   Estimated time: {route.estimated_time} minutes")
    print(f"   Feasible: {route.is_feasible}")
```

## 10. Comparison

| Algorithm | Time | Space | Best For |
|-----------|------|-------|----------|
| Basic Backtracking | O(n!) | O(n) | Small graphs |
| Warnsdorff | O(n²) avg | O(n) | Sparse graphs |
| Dynamic Programming | O(n² 2^n) | O(n 2^n) | n < 20 |
| Approximation | O(n³) | O(n²) | Large graphs |

## 11. Best Practices

1. **Check necessary conditions** before searching
2. **Use heuristics** (Warnsdorff) for sparse graphs
3. **Prune early** based on degree and connectivity
4. **Normalize cycles** when finding all solutions
5. **Consider approximation** for TSP variant

## 12. References

- [Wikipedia: Hamiltonian Path](https://en.wikipedia.org/wiki/Hamiltonian_path)
- [Wikipedia: Traveling Salesman Problem](https://en.wikipedia.org/wiki/Travelling_salesman_problem)
- Cormen, T. et al. "Introduction to Algorithms" - NP-Completeness
