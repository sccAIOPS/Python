# Minimum Spanning Tree

## Overview
- **Category**: Greedy Algorithm / Graph Theory
- **Complexity**: Prim's: O(E log V) | Kruskal's: O(E log E) | Space: O(V+E)
- **Type**: Tree construction, optimization
- **Source Files**: [graphs/minimum_spanning_tree.py](../../../graphs/minimum_spanning_tree.py)

## 1. Mathematical Foundation

### 1.1 Problem Definition

Given a connected, weighted, undirected graph $G = (V, E, w)$:
- $V$ = set of vertices
- $E$ = set of edges
- $w: E \rightarrow \mathbb{R}$ = weight function

**Objective**: Find a spanning tree $T$ with minimum total weight

$$
\text{Minimize } \sum_{e \in T} w(e)
$$

**Subject to**:
- $T$ connects all vertices
- $T$ contains exactly $|V| - 1$ edges
- $T$ has no cycles

### 1.2 Spanning Tree Properties

A spanning tree $T$ of graph $G$:
1. Contains all vertices of $G$
2. Is connected
3. Has no cycles
4. Has exactly $|V| - 1$ edges

### 1.3 Cut Property

**Theorem**: For any cut $(S, V-S)$ of graph $G$, the minimum weight edge crossing the cut must be in every MST.

$$
\text{If } e = \arg\min_{u \in S, v \in V-S} w(u, v), \text{ then } e \in MST
$$

### 1.4 Cycle Property

**Theorem**: For any cycle $C$ in graph $G$, the maximum weight edge in $C$ is not in any MST (unless multiple edges have same max weight).

### 1.5 Uniqueness

MST is unique if and only if all edge weights are distinct.

### 1.6 Number of Edges

For graph with $n$ vertices:
$$
|E_{MST}| = n - 1
$$

## 2. Algorithm Variants

### 2.1 Prim's Algorithm
Grow tree from single vertex, always add minimum edge.

### 2.2 Kruskal's Algorithm
Sort edges, add if no cycle formed (Union-Find).

### 2.3 Borůvka's Algorithm
Parallel-friendly, add minimum edge for each component.

### 2.4 Reverse-Delete
Start with all edges, remove maximum that doesn't disconnect.

## 3. Pseudocode

### 3.1 Prim's Algorithm

```
ALGORITHM Prim(G, start)
    INPUT: Connected weighted graph G, starting vertex
    OUTPUT: MST as set of edges
    
    MST ← {}
    visited ← {start}
    
    // Priority queue: (weight, u, v)
    edges ← MinHeap()
    
    // Add all edges from start vertex
    for each neighbor v of start do
        edges.insert((weight(start, v), start, v))
    
    while |visited| < |V| do
        // Get minimum weight edge
        (weight, u, v) ← edges.extractMin()
        
        // Skip if both endpoints already in tree
        if v in visited then
            continue
        
        // Add edge to MST
        MST.add((u, v, weight))
        visited.add(v)
        
        // Add edges from new vertex
        for each neighbor w of v do
            if w NOT in visited then
                edges.insert((weight(v, w), v, w))
    
    return MST
```

### 3.2 Kruskal's Algorithm

```
ALGORITHM Kruskal(G)
    INPUT: Connected weighted graph G
    OUTPUT: MST as set of edges
    
    MST ← {}
    
    // Sort all edges by weight
    sorted_edges ← Sort(E) by weight
    
    // Initialize Union-Find
    parent ← [i for i in 0...|V|-1]
    rank ← [0 for i in 0...|V|-1]
    
    FUNCTION Find(x)
        if parent[x] ≠ x then
            parent[x] ← Find(parent[x])
        return parent[x]
    
    FUNCTION Union(x, y)
        px, py ← Find(x), Find(y)
        if rank[px] < rank[py] then
            parent[px] ← py
        else if rank[px] > rank[py] then
            parent[py] ← px
        else
            parent[py] ← px
            rank[px] ← rank[px] + 1
    
    for each (u, v, weight) in sorted_edges do
        if Find(u) ≠ Find(v) then
            MST.add((u, v, weight))
            Union(u, v)
            
            // Early termination
            if |MST| = |V| - 1 then
                break
    
    return MST
```

### 3.3 Borůvka's Algorithm

```
ALGORITHM Boruvka(G)
    INPUT: Connected weighted graph G
    OUTPUT: MST as set of edges
    
    MST ← {}
    components ← |V|  // Each vertex is own component
    
    // Initialize Union-Find
    Initialize UF
    
    while components > 1 do
        cheapest ← array of size |V|, all None
        
        // Find cheapest edge for each component
        for each (u, v, weight) in E do
            cu, cv ← Find(u), Find(v)
            
            if cu ≠ cv then
                if cheapest[cu] = None OR weight < cheapest[cu].weight then
                    cheapest[cu] ← (u, v, weight)
                if cheapest[cv] = None OR weight < cheapest[cv].weight then
                    cheapest[cv] ← (u, v, weight)
        
        // Add cheapest edges to MST
        for each component c do
            if cheapest[c] ≠ None then
                (u, v, weight) ← cheapest[c]
                if Find(u) ≠ Find(v) then
                    MST.add((u, v, weight))
                    Union(u, v)
                    components ← components - 1
    
    return MST
```

## 4. Step-by-Step Example

### Example Graph

```
        2
    A ───── B
    │\      │
   4│ \3    │5
    │  \    │
    D ──── C
        1
```

Edges: A-B(2), A-C(3), A-D(4), B-C(5), C-D(1)

### 4.1 Prim's Algorithm (start at A)

```
Step 1: Start at A
  Visited: {A}
  Edges in heap: [(2,A,B), (3,A,C), (4,A,D)]
  MST: {}

Step 2: Extract min edge (2,A,B)
  Add B to tree
  Visited: {A, B}
  MST: {(A,B,2)}
  Add edges from B: [(5,B,C)]
  Heap: [(3,A,C), (4,A,D), (5,B,C)]

Step 3: Extract min edge (3,A,C)
  Add C to tree
  Visited: {A, B, C}
  MST: {(A,B,2), (A,C,3)}
  Add edges from C: [(1,C,D)]
  Heap: [(1,C,D), (4,A,D), (5,B,C)]

Step 4: Extract min edge (1,C,D)
  Add D to tree
  Visited: {A, B, C, D}
  MST: {(A,B,2), (A,C,3), (C,D,1)}
  
DONE! All vertices visited.
Total weight: 2 + 3 + 1 = 6
```

### 4.2 Kruskal's Algorithm

```
Step 1: Sort edges
  Sorted: [(C,D,1), (A,B,2), (A,C,3), (A,D,4), (B,C,5)]

Step 2: Process (C,D,1)
  Find(C) ≠ Find(D)? YES
  MST: {(C,D,1)}
  Union(C, D)

Step 3: Process (A,B,2)
  Find(A) ≠ Find(B)? YES
  MST: {(C,D,1), (A,B,2)}
  Union(A, B)

Step 4: Process (A,C,3)
  Find(A) ≠ Find(C)? YES
  MST: {(C,D,1), (A,B,2), (A,C,3)}
  Union(A, C)
  
|MST| = 3 = |V| - 1 → DONE!
Total weight: 1 + 2 + 3 = 6
```

### Visual MST

```
        2
    A ───── B
     \      
      \3    
       \    
    D ──── C
        1

MST edges: A-B(2), A-C(3), C-D(1)
Total weight: 6
```

## 5. Complexity Analysis

| Algorithm | Time | Space | Use Case |
|-----------|------|-------|----------|
| Prim (binary heap) | O(E log V) | O(V+E) | Dense graphs |
| Prim (Fibonacci heap) | O(E + V log V) | O(V+E) | Very dense |
| Kruskal | O(E log E) | O(V+E) | Sparse graphs |
| Borůvka | O(E log V) | O(V+E) | Parallel |

## 6. Visual Representation

### 6.1 Prim's Growth

```
Step 1:        Step 2:        Step 3:        Step 4:
    2              2              2              2
A───B          A═══B          A═══B          A═══B
│\  │          ║\  │          ║\  │          ║   │
4 3 5    →     4 3 5    →     4 ║3 5    →    4 ║  5
│  \│          │  \│          │  ║│          │  ║│
D───C          D───C          D──═C          D═══C
    1              1              1              1

Visited:       {A,B}          {A,B,C}        {A,B,C,D}
═ = MST edges
```

### 6.2 Kruskal's Union-Find

```
Initial:   A   B   C   D   (4 components)

After (C-D,1):  A   B   {C,D}   (3 components)

After (A-B,2):  {A,B}   {C,D}   (2 components)

After (A-C,3):  {A,B,C,D}       (1 component)

MST complete!
```

## 7. Implementation

```python
from typing import List, Tuple, Dict, Set, Optional
from dataclasses import dataclass, field
from collections import defaultdict
import heapq


@dataclass
class Edge:
    """Weighted edge."""
    u: int
    v: int
    weight: float
    
    def __lt__(self, other):
        return self.weight < other.weight


class UnionFind:
    """Disjoint Set Union for Kruskal's."""
    
    def __init__(self, n: int):
        self.parent = list(range(n))
        self.rank = [0] * n
    
    def find(self, x: int) -> int:
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
    
    def union(self, x: int, y: int) -> bool:
        px, py = self.find(x), self.find(y)
        if px == py:
            return False
        
        if self.rank[px] < self.rank[py]:
            px, py = py, px
        self.parent[py] = px
        if self.rank[px] == self.rank[py]:
            self.rank[px] += 1
        return True


class Graph:
    """Weighted undirected graph."""
    
    def __init__(self, vertices: int):
        self.V = vertices
        self.adj: Dict[int, List[Tuple[int, float]]] = defaultdict(list)
        self.edges: List[Edge] = []
    
    def add_edge(self, u: int, v: int, weight: float):
        """Add undirected edge."""
        self.adj[u].append((v, weight))
        self.adj[v].append((u, weight))
        self.edges.append(Edge(u, v, weight))
    
    def prim_mst(self, start: int = 0) -> Tuple[List[Edge], float]:
        """
        Prim's algorithm for MST.
        
        >>> g = Graph(4)
        >>> g.add_edge(0, 1, 2)
        >>> g.add_edge(0, 2, 3)
        >>> g.add_edge(0, 3, 4)
        >>> g.add_edge(1, 2, 5)
        >>> g.add_edge(2, 3, 1)
        >>> mst, weight = g.prim_mst()
        >>> weight
        6.0
        """
        visited = [False] * self.V
        mst = []
        total_weight = 0.0
        
        # Min-heap: (weight, from_vertex, to_vertex)
        heap = [(0, -1, start)]
        
        while heap and len(mst) < self.V - 1:
            weight, from_v, to_v = heapq.heappop(heap)
            
            if visited[to_v]:
                continue
            
            visited[to_v] = True
            
            if from_v != -1:
                mst.append(Edge(from_v, to_v, weight))
                total_weight += weight
            
            for neighbor, edge_weight in self.adj[to_v]:
                if not visited[neighbor]:
                    heapq.heappush(heap, (edge_weight, to_v, neighbor))
        
        return mst, total_weight
    
    def kruskal_mst(self) -> Tuple[List[Edge], float]:
        """
        Kruskal's algorithm for MST.
        
        >>> g = Graph(4)
        >>> g.add_edge(0, 1, 2)
        >>> g.add_edge(0, 2, 3)
        >>> g.add_edge(0, 3, 4)
        >>> g.add_edge(1, 2, 5)
        >>> g.add_edge(2, 3, 1)
        >>> mst, weight = g.kruskal_mst()
        >>> weight
        6.0
        """
        sorted_edges = sorted(self.edges)
        uf = UnionFind(self.V)
        
        mst = []
        total_weight = 0.0
        
        for edge in sorted_edges:
            if uf.union(edge.u, edge.v):
                mst.append(edge)
                total_weight += edge.weight
                
                if len(mst) == self.V - 1:
                    break
        
        return mst, total_weight
    
    def boruvka_mst(self) -> Tuple[List[Edge], float]:
        """
        Borůvka's algorithm for MST.
        
        >>> g = Graph(4)
        >>> g.add_edge(0, 1, 2)
        >>> g.add_edge(0, 2, 3)
        >>> g.add_edge(0, 3, 4)
        >>> g.add_edge(1, 2, 5)
        >>> g.add_edge(2, 3, 1)
        >>> mst, weight = g.boruvka_mst()
        >>> weight
        6.0
        """
        uf = UnionFind(self.V)
        mst = []
        total_weight = 0.0
        num_components = self.V
        
        while num_components > 1:
            # Find cheapest edge for each component
            cheapest = [None] * self.V
            
            for edge in self.edges:
                cu, cv = uf.find(edge.u), uf.find(edge.v)
                
                if cu != cv:
                    if cheapest[cu] is None or edge.weight < cheapest[cu].weight:
                        cheapest[cu] = edge
                    if cheapest[cv] is None or edge.weight < cheapest[cv].weight:
                        cheapest[cv] = edge
            
            # Add cheapest edges to MST
            for i in range(self.V):
                if cheapest[i] is not None:
                    edge = cheapest[i]
                    if uf.union(edge.u, edge.v):
                        mst.append(edge)
                        total_weight += edge.weight
                        num_components -= 1
        
        return mst, total_weight


def mst_weight(vertices: int, edges: List[Tuple[int, int, float]]) -> float:
    """
    Get MST weight for edge list.
    
    >>> edges = [(0,1,2), (0,2,3), (0,3,4), (1,2,5), (2,3,1)]
    >>> mst_weight(4, edges)
    6.0
    """
    g = Graph(vertices)
    for u, v, w in edges:
        g.add_edge(u, v, w)
    _, weight = g.kruskal_mst()
    return weight


def second_best_mst(
    vertices: int,
    edges: List[Tuple[int, int, float]]
) -> Optional[float]:
    """
    Find second-best MST weight.
    
    Try removing each MST edge and recalculating.
    """
    g = Graph(vertices)
    for u, v, w in edges:
        g.add_edge(u, v, w)
    
    mst, best_weight = g.kruskal_mst()
    mst_edge_set = {(min(e.u, e.v), max(e.u, e.v)) for e in mst}
    
    second_best = float('inf')
    
    for remove_edge in mst:
        # Create graph without this edge
        g2 = Graph(vertices)
        removed = (min(remove_edge.u, remove_edge.v), max(remove_edge.u, remove_edge.v))
        
        for u, v, w in edges:
            key = (min(u, v), max(u, v))
            if key != removed:
                g2.add_edge(u, v, w)
        
        # Try to find MST
        new_mst, new_weight = g2.kruskal_mst()
        
        if len(new_mst) == vertices - 1 and new_weight < second_best:
            second_best = new_weight
    
    return second_best if second_best != float('inf') else None


class MSTVisualizer:
    """Visualize MST construction."""
    
    def __init__(self, vertices: int, vertex_names: List[str] = None):
        self.V = vertices
        self.names = vertex_names or [str(i) for i in range(vertices)]
        self.edges = []
    
    def add_edge(self, u: int, v: int, weight: float):
        self.edges.append((u, v, weight))
    
    def visualize_kruskal(self) -> str:
        """Generate step-by-step visualization."""
        sorted_edges = sorted(self.edges, key=lambda e: e[2])
        uf = UnionFind(self.V)
        
        lines = ["Kruskal's Algorithm:", "=" * 40]
        lines.append(f"\nSorted edges: {[(self.names[u], self.names[v], w) for u, v, w in sorted_edges]}")
        lines.append("\nSteps:")
        
        mst = []
        step = 1
        
        for u, v, w in sorted_edges:
            pu, pv = uf.find(u), uf.find(v)
            
            if pu != pv:
                lines.append(f"  Step {step}: Add edge ({self.names[u]}-{self.names[v]}, {w})")
                uf.union(u, v)
                mst.append((u, v, w))
                step += 1
                
                if len(mst) == self.V - 1:
                    break
            else:
                lines.append(f"  Skip: ({self.names[u]}-{self.names[v]}, {w}) would create cycle")
        
        total = sum(w for _, _, w in mst)
        lines.append(f"\nMST edges: {[(self.names[u], self.names[v], w) for u, v, w in mst]}")
        lines.append(f"Total weight: {total}")
        
        return "\n".join(lines)


# Demo
if __name__ == "__main__":
    print("Minimum Spanning Tree Demo")
    print("=" * 50)
    
    # Create graph
    g = Graph(6)
    edges = [
        (0, 1, 4), (0, 2, 3),
        (1, 2, 1), (1, 3, 2),
        (2, 3, 4), (2, 4, 3),
        (3, 4, 2), (3, 5, 1),
        (4, 5, 4)
    ]
    
    for u, v, w in edges:
        g.add_edge(u, v, w)
    
    print("\n1. Graph edges:")
    for e in edges:
        print(f"   {e[0]} -- {e[1]} : {e[2]}")
    
    # Prim's
    print("\n2. Prim's Algorithm:")
    mst, weight = g.prim_mst(0)
    print(f"   MST weight: {weight}")
    print(f"   Edges: {[(e.u, e.v, e.weight) for e in mst]}")
    
    # Kruskal's
    print("\n3. Kruskal's Algorithm:")
    mst, weight = g.kruskal_mst()
    print(f"   MST weight: {weight}")
    print(f"   Edges: {[(e.u, e.v, e.weight) for e in mst]}")
    
    # Borůvka's
    print("\n4. Borůvka's Algorithm:")
    mst, weight = g.boruvka_mst()
    print(f"   MST weight: {weight}")
    print(f"   Edges: {[(e.u, e.v, e.weight) for e in mst]}")
    
    # Second-best MST
    print("\n5. Second-Best MST:")
    second = second_best_mst(6, edges)
    print(f"   Second-best weight: {second}")
    
    # Visualization
    print("\n6. Step-by-Step Visualization:")
    viz = MSTVisualizer(4, ['A', 'B', 'C', 'D'])
    viz.add_edge(0, 1, 2)
    viz.add_edge(0, 2, 3)
    viz.add_edge(0, 3, 4)
    viz.add_edge(1, 2, 5)
    viz.add_edge(2, 3, 1)
    print(viz.visualize_kruskal())
```

## 8. Applications

### 8.1 Common Use Cases

- **Network design**: Minimize cable/wire length
- **Cluster analysis**: Hierarchical clustering
- **Image segmentation**: Graph-based segmentation
- **Circuit design**: Minimize routing
- **Transportation**: Road network design

### 8.2 Related Problems

| Problem | Description |
|---------|-------------|
| Steiner Tree | MST with optional vertices |
| Maximum ST | Maximum weight spanning tree |
| k-MST | MST on k vertices |
| Constrained MST | MST with degree constraints |

## 9. Real-World Software Engineering Applications

### 9.1 Production Example: Network Infrastructure Optimizer

```python
from typing import Dict, List, Optional, Set, Tuple
from dataclasses import dataclass, field
from enum import Enum
from collections import defaultdict
import heapq
import json


class ConnectionType(Enum):
    FIBER = "fiber"
    COPPER = "copper"
    WIRELESS = "wireless"


@dataclass
class NetworkNode:
    """Network infrastructure node."""
    id: str
    name: str
    location: Tuple[float, float]  # (lat, lon)
    is_datacenter: bool = False
    capacity_gbps: float = 10.0


@dataclass
class NetworkLink:
    """Network connection between nodes."""
    source: str
    target: str
    distance_km: float
    connection_type: ConnectionType
    cost_per_km: float
    latency_ms: float
    bandwidth_gbps: float
    
    @property
    def total_cost(self) -> float:
        return self.distance_km * self.cost_per_km
    
    @property
    def weight(self) -> float:
        """Combined metric for optimization."""
        return self.total_cost


@dataclass
class NetworkDesign:
    """Result of network design optimization."""
    links: List[NetworkLink]
    total_cost: float
    total_latency: float
    is_connected: bool
    redundancy_links: List[NetworkLink] = field(default_factory=list)


class NetworkInfrastructureOptimizer:
    """
    Optimize network infrastructure using MST algorithms.
    
    Minimizes deployment cost while ensuring connectivity.
    """
    
    def __init__(self):
        self.nodes: Dict[str, NetworkNode] = {}
        self.potential_links: List[NetworkLink] = []
    
    def add_node(self, node: NetworkNode):
        """Add network node."""
        self.nodes[node.id] = node
    
    def add_potential_link(self, link: NetworkLink):
        """Add potential network link."""
        self.potential_links.append(link)
    
    def generate_all_links(
        self,
        connection_type: ConnectionType = ConnectionType.FIBER
    ):
        """Generate potential links between all node pairs."""
        node_list = list(self.nodes.values())
        
        cost_per_km = {
            ConnectionType.FIBER: 1000,
            ConnectionType.COPPER: 500,
            ConnectionType.WIRELESS: 200
        }
        
        for i in range(len(node_list)):
            for j in range(i + 1, len(node_list)):
                n1, n2 = node_list[i], node_list[j]
                
                distance = self._calculate_distance(n1.location, n2.location)
                
                link = NetworkLink(
                    source=n1.id,
                    target=n2.id,
                    distance_km=distance,
                    connection_type=connection_type,
                    cost_per_km=cost_per_km[connection_type],
                    latency_ms=distance * 0.01,  # ~10μs per km
                    bandwidth_gbps=100.0 if connection_type == ConnectionType.FIBER else 10.0
                )
                
                self.potential_links.append(link)
    
    def _calculate_distance(
        self,
        loc1: Tuple[float, float],
        loc2: Tuple[float, float]
    ) -> float:
        """Calculate distance between locations (simplified)."""
        # Haversine formula (simplified)
        from math import radians, cos, sin, sqrt, atan2
        
        lat1, lon1 = radians(loc1[0]), radians(loc1[1])
        lat2, lon2 = radians(loc2[0]), radians(loc2[1])
        
        dlat = lat2 - lat1
        dlon = lon2 - lon1
        
        a = sin(dlat/2)**2 + cos(lat1) * cos(lat2) * sin(dlon/2)**2
        c = 2 * atan2(sqrt(a), sqrt(1-a))
        
        return 6371 * c  # Earth radius in km
    
    def design_minimum_cost_network(self) -> NetworkDesign:
        """
        Design minimum cost network using Kruskal's algorithm.
        """
        if not self.nodes or not self.potential_links:
            return NetworkDesign([], 0, 0, False)
        
        # Sort links by cost
        sorted_links = sorted(self.potential_links, key=lambda l: l.total_cost)
        
        # Union-Find
        parent = {node_id: node_id for node_id in self.nodes}
        rank = {node_id: 0 for node_id in self.nodes}
        
        def find(x):
            if parent[x] != x:
                parent[x] = find(parent[x])
            return parent[x]
        
        def union(x, y):
            px, py = find(x), find(y)
            if px == py:
                return False
            if rank[px] < rank[py]:
                px, py = py, px
            parent[py] = px
            if rank[px] == rank[py]:
                rank[px] += 1
            return True
        
        # Build MST
        mst_links = []
        total_cost = 0.0
        total_latency = 0.0
        
        for link in sorted_links:
            if union(link.source, link.target):
                mst_links.append(link)
                total_cost += link.total_cost
                total_latency += link.latency_ms
                
                if len(mst_links) == len(self.nodes) - 1:
                    break
        
        is_connected = len(mst_links) == len(self.nodes) - 1
        
        return NetworkDesign(
            links=mst_links,
            total_cost=total_cost,
            total_latency=total_latency,
            is_connected=is_connected
        )
    
    def design_with_redundancy(
        self,
        redundancy_factor: float = 0.2
    ) -> NetworkDesign:
        """
        Design network with redundancy links.
        
        Adds extra links for fault tolerance.
        """
        base_design = self.design_minimum_cost_network()
        
        if not base_design.is_connected:
            return base_design
        
        # Find additional links for redundancy
        used_links = {(l.source, l.target) for l in base_design.links}
        used_links.update({(l.target, l.source) for l in base_design.links})
        
        # Sort remaining links by cost
        remaining = [
            l for l in self.potential_links
            if (l.source, l.target) not in used_links
        ]
        remaining.sort(key=lambda l: l.total_cost)
        
        # Add redundancy links
        num_redundant = max(1, int(len(base_design.links) * redundancy_factor))
        redundancy_links = remaining[:num_redundant]
        
        extra_cost = sum(l.total_cost for l in redundancy_links)
        
        return NetworkDesign(
            links=base_design.links,
            total_cost=base_design.total_cost + extra_cost,
            total_latency=base_design.total_latency,
            is_connected=True,
            redundancy_links=redundancy_links
        )
    
    def design_datacenter_centric(self) -> NetworkDesign:
        """
        Design network with datacenters as hubs.
        
        Uses modified Prim's starting from datacenters.
        """
        datacenters = [n for n in self.nodes.values() if n.is_datacenter]
        
        if not datacenters:
            return self.design_minimum_cost_network()
        
        # Build adjacency for Prim's
        adj = defaultdict(list)
        for link in self.potential_links:
            adj[link.source].append((link.target, link.total_cost, link))
            adj[link.target].append((link.source, link.total_cost, link))
        
        visited = set()
        mst_links = []
        total_cost = 0.0
        
        # Start from all datacenters
        heap = []
        for dc in datacenters:
            visited.add(dc.id)
            for neighbor, cost, link in adj[dc.id]:
                heapq.heappush(heap, (cost, link))
        
        while heap and len(visited) < len(self.nodes):
            cost, link = heapq.heappop(heap)
            
            # Determine which endpoint is new
            if link.source in visited and link.target in visited:
                continue
            
            new_node = link.target if link.source in visited else link.source
            visited.add(new_node)
            mst_links.append(link)
            total_cost += cost
            
            for neighbor, ncost, nlink in adj[new_node]:
                if neighbor not in visited:
                    heapq.heappush(heap, (ncost, nlink))
        
        total_latency = sum(l.latency_ms for l in mst_links)
        
        return NetworkDesign(
            links=mst_links,
            total_cost=total_cost,
            total_latency=total_latency,
            is_connected=len(visited) == len(self.nodes)
        )
    
    def estimate_deployment_timeline(
        self,
        design: NetworkDesign,
        team_size: int = 5
    ) -> Dict:
        """Estimate deployment timeline."""
        # Rough estimates
        km_per_day_per_team = {
            ConnectionType.FIBER: 0.5,
            ConnectionType.COPPER: 2.0,
            ConnectionType.WIRELESS: 10.0
        }
        
        total_km = sum(l.distance_km for l in design.links)
        avg_speed = sum(
            km_per_day_per_team.get(l.connection_type, 1.0)
            for l in design.links
        ) / max(1, len(design.links))
        
        days = total_km / (avg_speed * team_size)
        
        return {
            'total_km': total_km,
            'estimated_days': days,
            'team_size': team_size,
            'links_count': len(design.links)
        }


class ClusterAnalyzer:
    """
    Use MST for hierarchical clustering.
    """
    
    def __init__(self, points: List[Tuple[float, float]]):
        self.points = points
        self.n = len(points)
    
    def _distance(self, i: int, j: int) -> float:
        """Euclidean distance between points."""
        p1, p2 = self.points[i], self.points[j]
        return ((p1[0] - p2[0])**2 + (p1[1] - p2[1])**2)**0.5
    
    def build_mst(self) -> List[Tuple[int, int, float]]:
        """Build MST over points."""
        # Prim's algorithm
        visited = [False] * self.n
        mst = []
        
        # Start from point 0
        heap = [(0, -1, 0)]  # (weight, from, to)
        
        while heap and len(mst) < self.n - 1:
            weight, from_v, to_v = heapq.heappop(heap)
            
            if visited[to_v]:
                continue
            
            visited[to_v] = True
            
            if from_v != -1:
                mst.append((from_v, to_v, weight))
            
            for j in range(self.n):
                if not visited[j]:
                    heapq.heappush(heap, (self._distance(to_v, j), to_v, j))
        
        return mst
    
    def get_clusters(self, k: int) -> List[Set[int]]:
        """
        Get k clusters by removing k-1 longest MST edges.
        """
        mst = self.build_mst()
        
        # Sort by weight descending and remove k-1 edges
        sorted_mst = sorted(mst, key=lambda e: e[2], reverse=True)
        remaining = sorted_mst[k-1:]
        
        # Find connected components
        uf_parent = list(range(self.n))
        
        def find(x):
            if uf_parent[x] != x:
                uf_parent[x] = find(uf_parent[x])
            return uf_parent[x]
        
        for u, v, _ in remaining:
            pu, pv = find(u), find(v)
            if pu != pv:
                uf_parent[pu] = pv
        
        # Group by component
        clusters = defaultdict(set)
        for i in range(self.n):
            clusters[find(i)].add(i)
        
        return list(clusters.values())


# Demo
if __name__ == "__main__":
    print("Network Infrastructure Optimizer Demo")
    print("=" * 50)
    
    # Create optimizer
    optimizer = NetworkInfrastructureOptimizer()
    
    # Add cities (nodes)
    cities = [
        NetworkNode("NYC", "New York", (40.7128, -74.0060), is_datacenter=True),
        NetworkNode("LA", "Los Angeles", (34.0522, -118.2437), is_datacenter=True),
        NetworkNode("CHI", "Chicago", (41.8781, -87.6298)),
        NetworkNode("HOU", "Houston", (29.7604, -95.3698)),
        NetworkNode("PHX", "Phoenix", (33.4484, -112.0740)),
        NetworkNode("SEA", "Seattle", (47.6062, -122.3321)),
    ]
    
    for city in cities:
        optimizer.add_node(city)
    
    optimizer.generate_all_links(ConnectionType.FIBER)
    
    print("\n1. Minimum Cost Network:")
    design = optimizer.design_minimum_cost_network()
    
    print(f"   Total cost: ${design.total_cost:,.2f}")
    print(f"   Connected: {design.is_connected}")
    print(f"   Links:")
    for link in design.links:
        print(f"   - {link.source} -- {link.target}: ${link.total_cost:,.2f}")
    
    print("\n2. Network with Redundancy:")
    redundant_design = optimizer.design_with_redundancy(redundancy_factor=0.3)
    
    print(f"   Total cost: ${redundant_design.total_cost:,.2f}")
    print(f"   Redundancy links: {len(redundant_design.redundancy_links)}")
    
    print("\n3. Datacenter-Centric Design:")
    dc_design = optimizer.design_datacenter_centric()
    
    print(f"   Total cost: ${dc_design.total_cost:,.2f}")
    print(f"   Links: {len(dc_design.links)}")
    
    print("\n4. Deployment Timeline:")
    timeline = optimizer.estimate_deployment_timeline(design, team_size=10)
    print(f"   Total km: {timeline['total_km']:,.2f}")
    print(f"   Estimated days: {timeline['estimated_days']:.1f}")
    
    # Clustering demo
    print("\n5. MST-based Clustering:")
    points = [
        (1, 1), (1.5, 1.2), (1.2, 0.8),  # Cluster 1
        (5, 5), (5.2, 4.8), (4.8, 5.1),  # Cluster 2
        (8, 2), (8.1, 2.2), (7.9, 1.8),  # Cluster 3
    ]
    
    analyzer = ClusterAnalyzer(points)
    clusters = analyzer.get_clusters(3)
    
    print(f"   Points: {points}")
    print(f"   3 clusters: {[list(c) for c in clusters]}")
```

## 10. Comparison

| Algorithm | Time | Best For | Parallelizable |
|-----------|------|----------|----------------|
| Prim | O(E log V) | Dense graphs | No |
| Kruskal | O(E log E) | Sparse graphs | Partially |
| Borůvka | O(E log V) | Any | Yes |

## 11. Best Practices

1. **Choose algorithm** based on graph density
2. **Use Fibonacci heap** Prim for very dense graphs
3. **Path compression** essential for Union-Find
4. **Handle disconnected graphs** - check MST has V-1 edges
5. **Consider second-best MST** for robustness analysis

## 12. References

- [Wikipedia: Minimum Spanning Tree](https://en.wikipedia.org/wiki/Minimum_spanning_tree)
- Cormen, T. et al. "Introduction to Algorithms" - MST Algorithms
- Tarjan, R. "Data Structures and Network Algorithms"
