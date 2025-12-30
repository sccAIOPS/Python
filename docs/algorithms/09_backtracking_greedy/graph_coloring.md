# Graph Coloring

## Overview
- **Category**: Backtracking / Constraint Satisfaction
- **Complexity**: Time: O(k^n) worst case | Space: O(n)
- **Type**: NP-Complete problem
- **Source Files**: [backtracking/coloring.py](../../../backtracking/coloring.py)

## 1. Mathematical Foundation

### 1.1 Problem Definition

Given an undirected graph $G = (V, E)$ and $k$ colors:

**k-Coloring**: Assign colors $c: V \rightarrow \{1, 2, ..., k\}$ such that:
$$
\forall (u, v) \in E: c(u) \neq c(v)
$$

**Chromatic Number** $\chi(G)$: Minimum $k$ for which a k-coloring exists.

### 1.2 Bounds on Chromatic Number

**Lower bounds**:
- $\chi(G) \geq \omega(G)$ where $\omega(G)$ is the clique number
- $\chi(G) \geq \lceil n / \alpha(G) \rceil$ where $\alpha(G)$ is independence number

**Upper bounds**:
- $\chi(G) \leq \Delta(G) + 1$ where $\Delta(G)$ is maximum degree
- **Brooks' Theorem**: $\chi(G) \leq \Delta(G)$ unless $G$ is complete or odd cycle

### 1.3 Special Graph Classes

| Graph Type | Chromatic Number |
|------------|------------------|
| Empty graph $\bar{K_n}$ | 1 |
| Complete graph $K_n$ | $n$ |
| Path $P_n$ | 2 |
| Cycle $C_n$ | 2 if n even, 3 if n odd |
| Bipartite | 2 |
| Planar | $\leq 4$ (Four Color Theorem) |
| Tree | 2 |

### 1.4 Greedy Coloring

**Welsh-Powell algorithm**:
1. Order vertices by degree (descending)
2. Assign first available color to each vertex

Greedy uses at most $\Delta(G) + 1$ colors.

### 1.5 NP-Completeness

- Deciding if $\chi(G) \leq k$ is NP-Complete for $k \geq 3$
- 2-coloring is polynomial (bipartite check)

## 2. Algorithm Variants

### 2.1 Decision Problem
Is k-coloring possible?

### 2.2 Optimization Problem
Find minimum k (chromatic number).

### 2.3 Counting Problem
How many valid k-colorings exist?

### 2.4 List Coloring
Each vertex has a list of allowed colors.

## 3. Pseudocode

### 3.1 Backtracking

```
ALGORITHM GraphColoringBacktrack(graph, k)
    INPUT: Graph G, number of colors k
    OUTPUT: Valid k-coloring or None
    
    n ← number of vertices
    colors ← array of size n, initialized to 0
    
    FUNCTION IsSafe(vertex, color)
        for each neighbor of vertex do
            if colors[neighbor] = color then
                return false
        return true
    
    FUNCTION Backtrack(vertex)
        if vertex = n then
            return true  // All vertices colored
        
        for color ← 1 to k do
            if IsSafe(vertex, color) then
                colors[vertex] ← color
                
                if Backtrack(vertex + 1) then
                    return true
                
                colors[vertex] ← 0  // Backtrack
        
        return false
    
    if Backtrack(0) then
        return colors
    return None
```

### 3.2 With MRV Heuristic

```
ALGORITHM GraphColoringMRV(graph, k)
    INPUT: Graph G, number of colors k
    OUTPUT: Valid k-coloring or None
    
    n ← number of vertices
    colors ← array of size n, initialized to 0
    uncolored ← set of all vertices
    
    FUNCTION AvailableColors(vertex)
        available ← {1, 2, ..., k}
        for each neighbor of vertex do
            if colors[neighbor] ≠ 0 then
                available.remove(colors[neighbor])
        return available
    
    FUNCTION SelectUncoloredVertex()
        // MRV: Choose vertex with fewest available colors
        return min(uncolored, key=lambda v: |AvailableColors(v)|)
    
    FUNCTION Backtrack()
        if uncolored is empty then
            return true
        
        vertex ← SelectUncoloredVertex()
        uncolored.remove(vertex)
        
        for color in AvailableColors(vertex) do
            colors[vertex] ← color
            
            if Backtrack() then
                return true
            
            colors[vertex] ← 0
        
        uncolored.add(vertex)
        return false
    
    if Backtrack() then
        return colors
    return None
```

### 3.3 Greedy Coloring

```
ALGORITHM GreedyColoring(graph)
    INPUT: Graph G
    OUTPUT: Valid coloring (may not be optimal)
    
    n ← number of vertices
    colors ← array of size n, initialized to 0
    
    // Order vertices by degree (descending)
    vertices ← sort [0, 1, ..., n-1] by degree descending
    
    for each vertex in vertices do
        // Find colors used by neighbors
        used ← set()
        for each neighbor of vertex do
            if colors[neighbor] ≠ 0 then
                used.add(colors[neighbor])
        
        // Assign first available color
        color ← 1
        while color in used do
            color ← color + 1
        
        colors[vertex] ← color
    
    return colors
```

### 3.4 Find Chromatic Number

```
ALGORITHM ChromaticNumber(graph)
    INPUT: Graph G
    OUTPUT: Minimum colors needed
    
    n ← number of vertices
    
    // Try k = 1, 2, 3, ... until coloring succeeds
    for k ← 1 to n do
        if GraphColoringBacktrack(graph, k) ≠ None then
            return k
    
    return n  // Complete graph needs n colors
```

## 4. Step-by-Step Example

### Example: Pentagon Graph

```
Graph:
    0 ─── 1
    |     |
    |     |
    4     2
     ╲   ╱
      ╲ ╱
       3

Edges: (0,1), (1,2), (2,3), (3,4), (4,0)
```

**Finding 3-coloring:**

```
Try k = 2:
  Vertex 0: Color 1
  Vertex 1: Color 2
  Vertex 2: Color 1
  Vertex 3: Color 2
  Vertex 4: Must differ from 0(1) and 3(2) → FAIL
  
Try k = 3:
  Vertex 0: Color 1
  Vertex 1: Color 2 (differs from 0)
  Vertex 2: Color 1 (differs from 1)
  Vertex 3: Color 2 (differs from 2)
  Vertex 4: Color 3 (differs from 0, 3)
  SUCCESS!
```

**Result**: χ(Pentagon) = 3

```
    R ─── G
    |     |
    |     |
    B     R
     ╲   ╱
      ╲ ╱
       G

R=Red(1), G=Green(2), B=Blue(3)
```

## 5. Complexity Analysis

| Aspect | Complexity |
|--------|------------|
| Time (backtracking worst) | O(k^n) |
| Time (greedy) | O(n² + E) |
| Space | O(n + E) |
| Finding χ(G) | O(k^n) for each k |

### With MRV Heuristic

Average case improvement: Significant pruning, but still exponential worst case.

## 6. Visual Representation

### 6.1 State Space Tree

```
                        Start
                      ╱   │   ╲
                  V0=1  V0=2  V0=3
                  ╱│╲    │     │
              V1=2,3  ×  ...  ...
              ╱   ╲
          V2=1,3  ...
           ╱  ╲
       V3=2  ...
         │
       V4=3 ✓
```

### 6.2 Conflict Graph

```
Initial state:
    0[?]─── 1[?]
    │       │
    4[?]    2[?]
     ╲     ╱
      ╲   ╱
       3[?]

After coloring:
    0[R]─── 1[G]
    │       │
    4[B]    2[R]
     ╲     ╱
      ╲   ╱
       3[G]

No adjacent vertices share a color ✓
```

## 7. Implementation

```python
from typing import List, Optional, Set, Dict, Tuple
from collections import defaultdict


class Graph:
    """Graph for coloring algorithms."""
    
    def __init__(self, vertices: int):
        self.vertices = vertices
        self.adj: List[Set[int]] = [set() for _ in range(vertices)]
    
    def add_edge(self, u: int, v: int):
        self.adj[u].add(v)
        self.adj[v].add(u)
    
    def neighbors(self, v: int) -> Set[int]:
        return self.adj[v]
    
    def degree(self, v: int) -> int:
        return len(self.adj[v])
    
    def max_degree(self) -> int:
        return max(self.degree(v) for v in range(self.vertices))


def graph_coloring(graph: Graph, k: int) -> Optional[List[int]]:
    """
    Find k-coloring using backtracking.
    
    >>> g = Graph(5)
    >>> for i in range(5):
    ...     g.add_edge(i, (i+1) % 5)
    >>> colors = graph_coloring(g, 3)
    >>> colors is not None
    True
    >>> all(colors[u] != colors[v] for u in range(5) for v in g.neighbors(u))
    True
    """
    n = graph.vertices
    colors = [0] * n
    
    def is_safe(vertex: int, color: int) -> bool:
        for neighbor in graph.neighbors(vertex):
            if colors[neighbor] == color:
                return False
        return True
    
    def backtrack(vertex: int) -> bool:
        if vertex == n:
            return True
        
        for color in range(1, k + 1):
            if is_safe(vertex, color):
                colors[vertex] = color
                
                if backtrack(vertex + 1):
                    return True
                
                colors[vertex] = 0
        
        return False
    
    if backtrack(0):
        return colors
    return None


def graph_coloring_mrv(graph: Graph, k: int) -> Optional[List[int]]:
    """
    k-coloring with Minimum Remaining Values heuristic.
    
    >>> g = Graph(4)
    >>> for i in range(4):
    ...     for j in range(i+1, 4):
    ...         g.add_edge(i, j)
    >>> colors = graph_coloring_mrv(g, 4)
    >>> colors is not None
    True
    """
    n = graph.vertices
    colors = [0] * n
    uncolored = set(range(n))
    
    def available_colors(vertex: int) -> Set[int]:
        used = {colors[n] for n in graph.neighbors(vertex) if colors[n] > 0}
        return set(range(1, k + 1)) - used
    
    def select_vertex() -> int:
        # MRV: Choose vertex with fewest available colors
        return min(uncolored, key=lambda v: len(available_colors(v)))
    
    def backtrack() -> bool:
        if not uncolored:
            return True
        
        vertex = select_vertex()
        uncolored.remove(vertex)
        
        for color in available_colors(vertex):
            colors[vertex] = color
            
            if backtrack():
                return True
            
            colors[vertex] = 0
        
        uncolored.add(vertex)
        return False
    
    if backtrack():
        return colors
    return None


def greedy_coloring(graph: Graph) -> List[int]:
    """
    Greedy coloring (Welsh-Powell algorithm).
    
    >>> g = Graph(4)
    >>> g.add_edge(0, 1)
    >>> g.add_edge(1, 2)
    >>> g.add_edge(2, 3)
    >>> colors = greedy_coloring(g)
    >>> max(colors)  # Path graph needs 2 colors
    2
    """
    n = graph.vertices
    colors = [0] * n
    
    # Order by degree (descending)
    vertices = sorted(range(n), key=lambda v: -graph.degree(v))
    
    for vertex in vertices:
        # Find used colors
        used = {colors[n] for n in graph.neighbors(vertex) if colors[n] > 0}
        
        # First available color
        color = 1
        while color in used:
            color += 1
        
        colors[vertex] = color
    
    return colors


def chromatic_number(graph: Graph) -> int:
    """
    Find the chromatic number (minimum colors needed).
    
    >>> g = Graph(5)
    >>> for i in range(5):
    ...     g.add_edge(i, (i+1) % 5)
    >>> chromatic_number(g)  # Odd cycle needs 3 colors
    3
    """
    n = graph.vertices
    
    for k in range(1, n + 1):
        if graph_coloring_mrv(graph, k) is not None:
            return k
    
    return n


def is_bipartite(graph: Graph) -> bool:
    """
    Check if graph is bipartite (2-colorable).
    
    >>> g = Graph(4)
    >>> g.add_edge(0, 1)
    >>> g.add_edge(1, 2)
    >>> g.add_edge(2, 3)
    >>> is_bipartite(g)
    True
    """
    return graph_coloring(graph, 2) is not None


def count_colorings(graph: Graph, k: int) -> int:
    """
    Count the number of valid k-colorings.
    
    >>> g = Graph(3)
    >>> g.add_edge(0, 1)
    >>> g.add_edge(1, 2)
    >>> count_colorings(g, 3)  # Path of 3 vertices
    12
    """
    n = graph.vertices
    count = 0
    colors = [0] * n
    
    def is_safe(vertex: int, color: int) -> bool:
        return all(colors[n] != color for n in graph.neighbors(vertex))
    
    def backtrack(vertex: int):
        nonlocal count
        
        if vertex == n:
            count += 1
            return
        
        for color in range(1, k + 1):
            if is_safe(vertex, color):
                colors[vertex] = color
                backtrack(vertex + 1)
                colors[vertex] = 0
    
    backtrack(0)
    return count


class GraphColoring:
    """
    Comprehensive graph coloring solver.
    """
    
    def __init__(self, graph: Graph):
        self.graph = graph
        self.n = graph.vertices
    
    def color(self, k: Optional[int] = None) -> Optional[List[int]]:
        """
        Find valid coloring.
        
        If k is None, finds minimum coloring.
        """
        if k is None:
            k = chromatic_number(self.graph)
        
        return graph_coloring_mrv(self.graph, k)
    
    def chromatic_number(self) -> int:
        """Find minimum colors needed."""
        return chromatic_number(self.graph)
    
    def is_valid_coloring(self, colors: List[int]) -> bool:
        """Check if coloring is valid."""
        for u in range(self.n):
            for v in self.graph.neighbors(u):
                if colors[u] == colors[v]:
                    return False
        return True
    
    def chromatic_polynomial(self, k: int) -> int:
        """
        Calculate P(k), the number of valid k-colorings.
        
        Uses deletion-contraction for small graphs.
        """
        return count_colorings(self.graph, k)
```

## 8. Applications

### 8.1 Common Use Cases

- **Register allocation**: Assign variables to CPU registers
- **Scheduling**: Time slots for exams, meetings
- **Map coloring**: Adjacent regions different colors
- **Frequency assignment**: Non-interfering radio channels
- **Sudoku**: 9-coloring of specific graph

### 8.2 Related Problems

| Problem | Description |
|---------|-------------|
| k-Coloring | Decision version |
| Chromatic Number | Optimization version |
| Edge Coloring | Color edges, not vertices |
| List Coloring | Each vertex has color options |

## 9. Real-World Software Engineering Applications

### 9.1 Production Example: Exam Scheduler

```python
from typing import Dict, List, Optional, Set, Tuple
from dataclasses import dataclass, field
from datetime import datetime, time, timedelta
from enum import Enum


class ExamType(Enum):
    WRITTEN = "written"
    ORAL = "oral"
    PRACTICAL = "practical"


@dataclass
class Exam:
    """Represents an exam."""
    id: str
    course: str
    duration: int  # minutes
    students: Set[str]
    exam_type: ExamType = ExamType.WRITTEN
    required_rooms: int = 1
    
    def conflicts_with(self, other: 'Exam') -> bool:
        """Check if exams have common students."""
        return bool(self.students & other.students)


@dataclass
class TimeSlot:
    """Represents a time slot."""
    id: int
    date: datetime
    start_time: time
    end_time: time
    room_capacity: int = 100


@dataclass
class ScheduleResult:
    """Result of exam scheduling."""
    assignments: Dict[str, int]  # exam_id -> slot_id
    slots_used: int
    conflicts: List[Tuple[str, str]]
    unscheduled: List[str]


class ExamScheduler:
    """
    Schedule exams using graph coloring.
    
    Exams are vertices, conflicts are edges.
    Colors represent time slots.
    """
    
    def __init__(self, exams: List[Exam], slots: List[TimeSlot]):
        self.exams = {e.id: e for e in exams}
        self.slots = {s.id: s for s in slots}
        self.exam_list = exams
        self.n = len(exams)
        
        # Build conflict graph
        self.conflict_graph = self._build_conflict_graph()
    
    def _build_conflict_graph(self) -> Dict[str, Set[str]]:
        """Build graph where edges represent conflicts."""
        graph = {e.id: set() for e in self.exam_list}
        
        for i, exam1 in enumerate(self.exam_list):
            for exam2 in self.exam_list[i+1:]:
                if exam1.conflicts_with(exam2):
                    graph[exam1.id].add(exam2.id)
                    graph[exam2.id].add(exam1.id)
        
        return graph
    
    def schedule(self) -> ScheduleResult:
        """
        Schedule all exams to time slots.
        
        Uses graph coloring to avoid conflicts.
        """
        # Create internal graph
        exam_ids = list(self.exams.keys())
        id_to_idx = {eid: i for i, eid in enumerate(exam_ids)}
        
        g = Graph(self.n)
        for eid, conflicts in self.conflict_graph.items():
            for cid in conflicts:
                if id_to_idx[eid] < id_to_idx[cid]:
                    g.add_edge(id_to_idx[eid], id_to_idx[cid])
        
        # Try to color with available slots
        k = len(self.slots)
        colors = graph_coloring_mrv(g, k)
        
        if colors is None:
            # Try to find minimum slots needed
            min_slots = chromatic_number(g)
            return ScheduleResult(
                assignments={},
                slots_used=0,
                conflicts=[],
                unscheduled=exam_ids
            )
        
        # Map colors to time slots
        slot_ids = list(self.slots.keys())
        assignments = {
            exam_ids[i]: slot_ids[colors[i] - 1]
            for i in range(self.n)
        }
        
        return ScheduleResult(
            assignments=assignments,
            slots_used=max(colors),
            conflicts=[],
            unscheduled=[]
        )
    
    def schedule_with_preferences(
        self,
        preferences: Dict[str, List[int]]  # exam_id -> preferred slot_ids
    ) -> ScheduleResult:
        """
        Schedule considering exam preferences.
        
        Uses list coloring approach.
        """
        exam_ids = list(self.exams.keys())
        assignments = {}
        unscheduled = []
        
        # Order by constraint (fewer preferences first)
        ordered_exams = sorted(
            exam_ids,
            key=lambda eid: len(preferences.get(eid, list(self.slots.keys())))
        )
        
        for eid in ordered_exams:
            # Get available slots (not used by conflicting exams)
            used = {
                assignments[cid]
                for cid in self.conflict_graph[eid]
                if cid in assignments
            }
            
            # Preferred slots
            allowed = preferences.get(eid, list(self.slots.keys()))
            available = [s for s in allowed if s not in used]
            
            if available:
                assignments[eid] = available[0]
            else:
                unscheduled.append(eid)
        
        return ScheduleResult(
            assignments=assignments,
            slots_used=len(set(assignments.values())),
            conflicts=[],
            unscheduled=unscheduled
        )
    
    def minimum_slots_needed(self) -> int:
        """Calculate minimum time slots needed."""
        exam_ids = list(self.exams.keys())
        id_to_idx = {eid: i for i, eid in enumerate(exam_ids)}
        
        g = Graph(self.n)
        for eid, conflicts in self.conflict_graph.items():
            for cid in conflicts:
                if id_to_idx[eid] < id_to_idx[cid]:
                    g.add_edge(id_to_idx[eid], id_to_idx[cid])
        
        return chromatic_number(g)
    
    def get_conflict_density(self) -> float:
        """Calculate conflict density (edges/possible edges)."""
        edges = sum(len(conflicts) for conflicts in self.conflict_graph.values()) // 2
        max_edges = self.n * (self.n - 1) // 2
        return edges / max_edges if max_edges > 0 else 0


class RegisterAllocator:
    """
    CPU register allocation using graph coloring.
    
    Variables are vertices, interference is edges.
    Colors represent registers.
    """
    
    def __init__(self, num_registers: int):
        self.num_registers = num_registers
    
    def allocate(
        self,
        variables: List[str],
        interference: List[Tuple[str, str]]
    ) -> Optional[Dict[str, int]]:
        """
        Allocate variables to registers.
        
        Returns mapping variable -> register, or None if spilling needed.
        """
        n = len(variables)
        var_to_idx = {v: i for i, v in enumerate(variables)}
        
        g = Graph(n)
        for v1, v2 in interference:
            if v1 in var_to_idx and v2 in var_to_idx:
                g.add_edge(var_to_idx[v1], var_to_idx[v2])
        
        colors = graph_coloring_mrv(g, self.num_registers)
        
        if colors is None:
            return None
        
        return {
            variables[i]: colors[i]
            for i in range(n)
        }
    
    def allocate_with_spilling(
        self,
        variables: List[str],
        interference: List[Tuple[str, str]]
    ) -> Tuple[Dict[str, int], List[str]]:
        """
        Allocate with spilling for excess variables.
        
        Returns (allocation, spilled_variables).
        """
        allocation = self.allocate(variables, interference)
        
        if allocation is not None:
            return allocation, []
        
        # Need to spill - use heuristic to choose
        # Spill variables with highest degree
        var_degree = defaultdict(int)
        for v1, v2 in interference:
            var_degree[v1] += 1
            var_degree[v2] += 1
        
        spilled = []
        remaining = list(variables)
        remaining_interference = list(interference)
        
        while True:
            # Try allocation
            allocation = self.allocate(remaining, remaining_interference)
            if allocation is not None:
                return allocation, spilled
            
            # Spill highest degree variable
            if not remaining:
                break
            
            to_spill = max(remaining, key=lambda v: var_degree[v])
            spilled.append(to_spill)
            remaining.remove(to_spill)
            remaining_interference = [
                (v1, v2) for v1, v2 in remaining_interference
                if v1 != to_spill and v2 != to_spill
            ]
        
        return {}, spilled


# Demo
if __name__ == "__main__":
    print("Graph Coloring Demo")
    print("=" * 50)
    
    # Basic coloring
    print("\n1. Basic Graph Coloring:")
    g = Graph(5)
    for i in range(5):
        g.add_edge(i, (i+1) % 5)
    
    colors = graph_coloring(g, 3)
    print(f"   Pentagon graph (odd cycle)")
    print(f"   Colors: {colors}")
    print(f"   Chromatic number: {chromatic_number(g)}")
    
    # Bipartite
    print("\n2. Bipartite Graph:")
    g2 = Graph(4)
    g2.add_edge(0, 2)
    g2.add_edge(0, 3)
    g2.add_edge(1, 2)
    g2.add_edge(1, 3)
    
    print(f"   Is bipartite: {is_bipartite(g2)}")
    print(f"   2-coloring: {graph_coloring(g2, 2)}")
    
    # Complete graph
    print("\n3. Complete Graph K4:")
    g3 = Graph(4)
    for i in range(4):
        for j in range(i+1, 4):
            g3.add_edge(i, j)
    
    print(f"   Chromatic number: {chromatic_number(g3)}")
    print(f"   Number of 4-colorings: {count_colorings(g3, 4)}")
    
    # Exam scheduling
    print("\n4. Exam Scheduler:")
    exams = [
        Exam("E1", "Math 101", 120, {"S1", "S2", "S3"}),
        Exam("E2", "Physics 101", 120, {"S2", "S4", "S5"}),
        Exam("E3", "CS 101", 120, {"S1", "S4", "S6"}),
        Exam("E4", "Chemistry", 120, {"S7", "S8"}),
    ]
    
    slots = [
        TimeSlot(1, datetime(2024, 1, 15), time(9, 0), time(11, 0)),
        TimeSlot(2, datetime(2024, 1, 15), time(14, 0), time(16, 0)),
        TimeSlot(3, datetime(2024, 1, 16), time(9, 0), time(11, 0)),
    ]
    
    scheduler = ExamScheduler(exams, slots)
    result = scheduler.schedule()
    
    print(f"   Exams: {[e.course for e in exams]}")
    print(f"   Minimum slots needed: {scheduler.minimum_slots_needed()}")
    print(f"   Assignments: {result.assignments}")
    print(f"   Slots used: {result.slots_used}")
    
    # Register allocation
    print("\n5. Register Allocator:")
    allocator = RegisterAllocator(3)
    
    variables = ["a", "b", "c", "d"]
    interference = [("a", "b"), ("b", "c"), ("c", "d")]
    
    allocation = allocator.allocate(variables, interference)
    print(f"   Variables: {variables}")
    print(f"   Interference: {interference}")
    print(f"   Allocation: {allocation}")
```

## 10. Comparison

| Method | Time | Quality | Best For |
|--------|------|---------|----------|
| Backtracking | O(k^n) | Optimal | Small graphs, finding χ |
| MRV Backtracking | O(k^n) | Optimal | Medium graphs |
| Greedy | O(n² + E) | ≤ Δ+1 colors | Large graphs |
| DSatur | O(n²) | Good | General purpose |

## 11. Best Practices

1. **Check bipartiteness** first (O(n+E))
2. **Use degree ordering** in greedy
3. **Apply MRV heuristic** in backtracking
4. **Compute bounds** before searching
5. **Consider approximation** for large graphs

## 12. References

- [Wikipedia: Graph Coloring](https://en.wikipedia.org/wiki/Graph_coloring)
- [Wikipedia: Four Color Theorem](https://en.wikipedia.org/wiki/Four_color_theorem)
- Cormen, T. et al. "Introduction to Algorithms" - Graph Algorithms
