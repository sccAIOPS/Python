# DBSCAN (Density-Based Spatial Clustering of Applications with Noise)

## Overview
- **Category**: Clustering / Unsupervised Learning
- **Complexity**: Average: O(n log n) | Worst: O(n²)
- **Type**: Density-based clustering algorithm
- **Source Files**: [machine_learning/dbscan.py](../../../machine_learning/dbscan.py)

## 1. Mathematical Foundation

### 1.1 Core Definitions

**ε-neighborhood** of point $p$:
$$
N_\epsilon(p) = \{q \in D : d(p, q) \leq \epsilon\}
$$

**Core point**: Point $p$ where $|N_\epsilon(p)| \geq \text{MinPts}$

**Border point**: Not a core point but in neighborhood of a core point

**Noise point**: Neither core nor border point

### 1.2 Density Connectivity

**Directly density-reachable**: Point $p$ is directly density-reachable from $q$ if:
1. $p \in N_\epsilon(q)$
2. $|N_\epsilon(q)| \geq \text{MinPts}$ (q is a core point)

**Density-reachable**: Point $p$ is density-reachable from $q$ if there exists a chain:
$$
p_1, p_2, ..., p_n \text{ where } p_1 = q, p_n = p
$$
and each $p_{i+1}$ is directly density-reachable from $p_i$.

**Density-connected**: Points $p$ and $q$ are density-connected if there exists point $o$ such that both $p$ and $q$ are density-reachable from $o$.

### 1.3 Cluster Definition

A cluster $C$ is a non-empty subset of $D$ satisfying:
1. **Maximality**: If $p \in C$ and $q$ is density-reachable from $p$, then $q \in C$
2. **Connectivity**: For any $p, q \in C$, $p$ and $q$ are density-connected

### 1.4 Distance Metrics

**Euclidean distance** (default):
$$
d(p, q) = \sqrt{\sum_{i=1}^{d}(p_i - q_i)^2}
$$

**Manhattan distance**:
$$
d(p, q) = \sum_{i=1}^{d}|p_i - q_i|
$$

**Minkowski distance**:
$$
d(p, q) = \left(\sum_{i=1}^{d}|p_i - q_i|^n\right)^{1/n}
$$

**Haversine distance** (geographic):
$$
d(p, q) = 2r \arcsin\left(\sqrt{\sin^2\left(\frac{\phi_2-\phi_1}{2}\right) + \cos(\phi_1)\cos(\phi_2)\sin^2\left(\frac{\lambda_2-\lambda_1}{2}\right)}\right)
$$

### 1.5 Parameter Selection

**Epsilon (ε)**: Use k-distance graph
- Plot sorted distances to k-th nearest neighbor
- Look for "elbow" (sharp change in slope)

**MinPts**: Rule of thumb
$$
\text{MinPts} \geq d + 1 \text{ (where d is dimensionality)}
$$
- For 2D data: MinPts = 4 is common
- For higher dimensions: MinPts = 2 × d

## 2. Pseudocode

```
ALGORITHM DBSCAN(D, ε, MinPts)
    INPUT: Dataset D, radius ε, minimum points MinPts
    OUTPUT: Cluster labels for each point
    
    C ← 0  // Cluster counter
    labels ← array of UNDEFINED for all points
    
    for each point P in D do
        if labels[P] ≠ UNDEFINED then
            continue  // Already processed
        
        // Find neighbors within ε
        neighbors ← RegionQuery(D, P, ε)
        
        if |neighbors| < MinPts then
            labels[P] ← NOISE
        else
            C ← C + 1  // Start new cluster
            ExpandCluster(D, P, neighbors, C, ε, MinPts, labels)
    
    return labels

ALGORITHM ExpandCluster(D, P, neighbors, C, ε, MinPts, labels)
    INPUT: Database D, point P, neighbors, cluster ID C, parameters
    OUTPUT: Expanded cluster (modifies labels in place)
    
    labels[P] ← C  // Assign P to cluster C
    
    // Use seed set for BFS-like expansion
    seedSet ← neighbors
    i ← 0
    
    while i < |seedSet| do
        Q ← seedSet[i]
        
        if labels[Q] = NOISE then
            labels[Q] ← C  // Change noise to border point
        
        if labels[Q] ≠ UNDEFINED then
            i ← i + 1
            continue
        
        labels[Q] ← C
        
        // Find Q's neighbors
        neighborsQ ← RegionQuery(D, Q, ε)
        
        if |neighborsQ| ≥ MinPts then
            // Q is a core point, add its neighbors to seed set
            seedSet ← seedSet ∪ neighborsQ
        
        i ← i + 1

ALGORITHM RegionQuery(D, P, ε)
    INPUT: Database D, query point P, radius ε
    OUTPUT: Set of points within ε distance of P
    
    neighbors ← {}
    
    for each point Q in D do
        if distance(P, Q) ≤ ε then
            neighbors ← neighbors ∪ {Q}
    
    return neighbors

ALGORITHM OptimizedRegionQuery_KDTree(tree, P, ε)
    INPUT: KD-tree, query point P, radius ε
    OUTPUT: Points within ε of P
    
    return tree.query_ball_point(P, ε)
```

### Parameter Selection Algorithm

```
ALGORITHM FindOptimalEpsilon(D, k)
    INPUT: Dataset D, k (usually MinPts - 1)
    OUTPUT: Suggested ε value
    
    // Compute k-distances for all points
    k_distances ← []
    
    for each point P in D do
        distances ← sort([distance(P, Q) for Q in D - {P}])
        k_distances.append(distances[k])
    
    // Sort k-distances
    k_distances ← sort(k_distances)
    
    // Find elbow using curvature
    n ← |k_distances|
    
    // Line from first to last point
    start ← (0, k_distances[0])
    end ← (n-1, k_distances[n-1])
    
    max_distance ← 0
    elbow_index ← 0
    
    for i ← 1 to n-2 do
        point ← (i, k_distances[i])
        
        // Distance from point to line
        dist ← PerpendicularDistance(point, start, end)
        
        if dist > max_distance then
            max_distance ← dist
            elbow_index ← i
    
    return k_distances[elbow_index]
```

## 3. Step-by-Step Example

### Example Dataset

| Point | X | Y |
|-------|---|---|
| A | 1 | 1 |
| B | 1.5 | 1.2 |
| C | 1.2 | 0.8 |
| D | 5 | 5 |
| E | 5.2 | 5.1 |
| F | 5.1 | 4.8 |
| G | 10 | 10 |

**Parameters**: ε = 1.0, MinPts = 2

### Step 1: Process Point A
- N_ε(A) = {A, B, C} (distance A-B=0.54, A-C=0.28)
- |N_ε(A)| = 3 ≥ MinPts = 2 → A is core point
- Start Cluster 1, expand

### Step 2: Expand from A
- Add B, C to Cluster 1
- Check B: N_ε(B) = {A, B, C} → B is core, already in cluster
- Check C: N_ε(C) = {A, B, C} → C is core, already in cluster

### Step 3: Process Point D
- Already assigned? No
- N_ε(D) = {D, E, F}
- Start Cluster 2, expand

### Step 4: Process Point G
- N_ε(G) = {G} only
- |N_ε(G)| = 1 < MinPts → G is NOISE

### Final Result
- **Cluster 1**: {A, B, C}
- **Cluster 2**: {D, E, F}
- **Noise**: {G}

## 4. Complexity Analysis

| Operation | Time Complexity | Space Complexity |
|-----------|----------------|------------------|
| Naive RegionQuery | O(n) per point | O(n) |
| Total (naive) | O(n²) | O(n) |
| With KD-tree | O(n log n) avg | O(n) |
| With spatial index | O(n log n) | O(n) |

**Space**: O(n) for labels + O(n) for spatial index

## 5. Visual Representation

### 5.1 DBSCAN Concept

```
                    Epsilon (ε) radius
                         ↓
    ●────────────────────┐
    │  ●    ●           │
    │     ●    ●        │ Cluster 1
    │  ●       ●        │ (Core + Border)
    │                   │
    └───────────────────┘
    
                    ○  ← Noise point
                         (isolated)
    
    ┌───────────────────┐
    │     ●    ●        │
    │  ●    ●    ●      │ Cluster 2
    │     ●             │
    └───────────────────┘

Legend:
● Core point (≥ MinPts neighbors)
◐ Border point (< MinPts, near core)
○ Noise (isolated)
```

### 5.2 Core, Border, and Noise Points

```
           ε
        ┌──┴──┐
        
    ○   │  ●  │      Core: has ≥ MinPts
        │  │  │      points in ε radius
  ◐─────┤  ●  ├─────◐
        │  │  │      Border: < MinPts but
        │  ●  │      reachable from core
        └──┬──┘
           │         Noise: neither core
    ○      ○         nor border

MinPts = 3
```

### 5.3 Density Reachability

```
Density-Reachable Chain:
    
    (Core)    (Core)    (Core)    (Border)
      ●─────────●─────────●─────────◐
      p₁        p₂        p₃        p₄
      
    p₄ is density-reachable from p₁
    
Not Symmetric:
    
      ●─────────◐
    (Core)   (Border)
    
    Border is reachable FROM core
    Core is NOT reachable from border
```

## 6. Implementation

```python
from typing import List, Set, Tuple, Optional, Dict
import math
from collections import deque


class DBSCAN:
    """
    DBSCAN clustering algorithm.
    
    Density-Based Spatial Clustering of Applications with Noise.
    """
    
    # Label constants
    UNDEFINED = -2
    NOISE = -1
    
    def __init__(
        self,
        eps: float = 0.5,
        min_samples: int = 5,
        metric: str = 'euclidean'
    ):
        """
        Initialize DBSCAN.
        
        Args:
            eps: Maximum distance for neighborhood
            min_samples: Minimum points to form dense region
            metric: Distance metric ('euclidean', 'manhattan')
        
        >>> dbscan = DBSCAN(eps=0.5, min_samples=3)
        >>> dbscan.eps
        0.5
        """
        self.eps = eps
        self.min_samples = min_samples
        self.metric = metric
        
        self.labels_: List[int] = []
        self.core_sample_indices_: List[int] = []
        self.n_clusters_: int = 0
    
    def _distance(
        self,
        p1: List[float],
        p2: List[float]
    ) -> float:
        """Calculate distance between two points."""
        if self.metric == 'euclidean':
            return math.sqrt(sum((a - b) ** 2 for a, b in zip(p1, p2)))
        elif self.metric == 'manhattan':
            return sum(abs(a - b) for a, b in zip(p1, p2))
        else:
            # Default to Euclidean
            return math.sqrt(sum((a - b) ** 2 for a, b in zip(p1, p2)))
    
    def _region_query(
        self,
        X: List[List[float]],
        point_idx: int
    ) -> List[int]:
        """Find all points within eps of point_idx."""
        neighbors = []
        point = X[point_idx]
        
        for i, other in enumerate(X):
            if self._distance(point, other) <= self.eps:
                neighbors.append(i)
        
        return neighbors
    
    def _expand_cluster(
        self,
        X: List[List[float]],
        point_idx: int,
        neighbors: List[int],
        cluster_id: int,
        labels: List[int]
    ):
        """Expand cluster from a core point."""
        labels[point_idx] = cluster_id
        
        # Use deque for efficient BFS
        seed_set = deque(neighbors)
        
        while seed_set:
            current_idx = seed_set.popleft()
            
            # Change noise to border point
            if labels[current_idx] == self.NOISE:
                labels[current_idx] = cluster_id
            
            # Skip if already processed
            if labels[current_idx] != self.UNDEFINED:
                continue
            
            # Assign to cluster
            labels[current_idx] = cluster_id
            
            # Find neighbors of current point
            current_neighbors = self._region_query(X, current_idx)
            
            # If current is a core point, add its neighbors
            if len(current_neighbors) >= self.min_samples:
                self.core_sample_indices_.append(current_idx)
                for neighbor in current_neighbors:
                    if labels[neighbor] == self.UNDEFINED:
                        seed_set.append(neighbor)
    
    def fit(self, X: List[List[float]]) -> 'DBSCAN':
        """
        Perform DBSCAN clustering.
        
        >>> dbscan = DBSCAN(eps=1.5, min_samples=2)
        >>> X = [[0, 0], [0.5, 0.5], [1, 1], [5, 5], [5.5, 5.5], [10, 10]]
        >>> dbscan.fit(X)
        <...>
        >>> dbscan.n_clusters_
        2
        """
        n_samples = len(X)
        labels = [self.UNDEFINED] * n_samples
        self.core_sample_indices_ = []
        
        cluster_id = 0
        
        for i in range(n_samples):
            # Skip if already processed
            if labels[i] != self.UNDEFINED:
                continue
            
            # Find neighbors
            neighbors = self._region_query(X, i)
            
            if len(neighbors) < self.min_samples:
                # Mark as noise (may change later if border)
                labels[i] = self.NOISE
            else:
                # Start new cluster
                self.core_sample_indices_.append(i)
                self._expand_cluster(X, i, neighbors, cluster_id, labels)
                cluster_id += 1
        
        self.labels_ = labels
        self.n_clusters_ = cluster_id
        
        return self
    
    def fit_predict(self, X: List[List[float]]) -> List[int]:
        """
        Fit and return cluster labels.
        
        >>> dbscan = DBSCAN(eps=1.0, min_samples=2)
        >>> X = [[0, 0], [0.5, 0], [3, 3], [3.5, 3]]
        >>> labels = dbscan.fit_predict(X)
        >>> labels[0] == labels[1]  # Same cluster
        True
        >>> labels[0] != labels[2]  # Different clusters
        True
        """
        self.fit(X)
        return self.labels_


class DBSCANOptimized:
    """
    DBSCAN with KD-tree optimization for faster neighbor search.
    """
    
    UNDEFINED = -2
    NOISE = -1
    
    def __init__(
        self,
        eps: float = 0.5,
        min_samples: int = 5
    ):
        self.eps = eps
        self.min_samples = min_samples
        
        self.labels_: List[int] = []
        self.core_sample_indices_: List[int] = []
        self.n_clusters_: int = 0
        self._tree: Optional['KDTree'] = None
    
    def fit(self, X: List[List[float]]) -> 'DBSCANOptimized':
        """Fit with KD-tree optimization."""
        n_samples = len(X)
        
        # Build KD-tree
        self._tree = KDTree(X)
        
        labels = [self.UNDEFINED] * n_samples
        self.core_sample_indices_ = []
        cluster_id = 0
        
        for i in range(n_samples):
            if labels[i] != self.UNDEFINED:
                continue
            
            # Query neighbors using KD-tree
            neighbors = self._tree.query_radius(X[i], self.eps)
            
            if len(neighbors) < self.min_samples:
                labels[i] = self.NOISE
            else:
                self.core_sample_indices_.append(i)
                self._expand_cluster(X, i, neighbors, cluster_id, labels)
                cluster_id += 1
        
        self.labels_ = labels
        self.n_clusters_ = cluster_id
        
        return self
    
    def _expand_cluster(
        self,
        X: List[List[float]],
        point_idx: int,
        neighbors: List[int],
        cluster_id: int,
        labels: List[int]
    ):
        """Expand cluster using KD-tree queries."""
        labels[point_idx] = cluster_id
        
        seed_set = deque(neighbors)
        
        while seed_set:
            current_idx = seed_set.popleft()
            
            if labels[current_idx] == self.NOISE:
                labels[current_idx] = cluster_id
            
            if labels[current_idx] != self.UNDEFINED:
                continue
            
            labels[current_idx] = cluster_id
            
            # Use KD-tree for neighbor query
            current_neighbors = self._tree.query_radius(X[current_idx], self.eps)
            
            if len(current_neighbors) >= self.min_samples:
                self.core_sample_indices_.append(current_idx)
                for neighbor in current_neighbors:
                    if labels[neighbor] == self.UNDEFINED:
                        seed_set.append(neighbor)
    
    def fit_predict(self, X: List[List[float]]) -> List[int]:
        self.fit(X)
        return self.labels_


class KDTree:
    """Simple KD-tree for radius queries."""
    
    def __init__(self, points: List[List[float]]):
        self.points = points
        self.n_dims = len(points[0]) if points else 0
        self.root = self._build(list(range(len(points))), 0)
    
    def _build(self, indices: List[int], depth: int) -> Optional[Dict]:
        if not indices:
            return None
        
        axis = depth % self.n_dims
        
        # Sort by current axis
        indices.sort(key=lambda i: self.points[i][axis])
        mid = len(indices) // 2
        
        return {
            'idx': indices[mid],
            'axis': axis,
            'left': self._build(indices[:mid], depth + 1),
            'right': self._build(indices[mid + 1:], depth + 1)
        }
    
    def query_radius(
        self,
        query: List[float],
        radius: float
    ) -> List[int]:
        """Find all points within radius of query."""
        results = []
        self._query_radius(self.root, query, radius, results)
        return results
    
    def _query_radius(
        self,
        node: Optional[Dict],
        query: List[float],
        radius: float,
        results: List[int]
    ):
        if node is None:
            return
        
        point = self.points[node['idx']]
        axis = node['axis']
        
        # Check if current point is within radius
        dist = math.sqrt(sum((a - b) ** 2 for a, b in zip(query, point)))
        if dist <= radius:
            results.append(node['idx'])
        
        # Check which branches to explore
        diff = query[axis] - point[axis]
        
        if diff <= radius:
            self._query_radius(node['left'], query, radius, results)
        if diff >= -radius:
            self._query_radius(node['right'], query, radius, results)


def find_optimal_eps(
    X: List[List[float]],
    k: int = 4
) -> Tuple[float, List[float]]:
    """
    Find optimal epsilon using k-distance graph.
    
    >>> X = [[0, 0], [0.5, 0], [1, 0], [5, 5], [5.5, 5], [6, 5]]
    >>> eps, _ = find_optimal_eps(X, k=2)
    >>> 0.5 < eps < 2.0
    True
    """
    n = len(X)
    k_distances = []
    
    for i in range(n):
        # Compute distances to all other points
        distances = []
        for j in range(n):
            if i != j:
                dist = math.sqrt(sum((X[i][d] - X[j][d]) ** 2 
                                    for d in range(len(X[i]))))
                distances.append(dist)
        
        distances.sort()
        k_distances.append(distances[min(k - 1, len(distances) - 1)])
    
    # Sort k-distances
    k_distances.sort()
    
    # Find elbow using maximum curvature
    # Simple approach: find point with maximum distance to line
    n_pts = len(k_distances)
    
    # Line from first to last point
    x1, y1 = 0, k_distances[0]
    x2, y2 = n_pts - 1, k_distances[-1]
    
    max_dist = 0
    elbow_idx = n_pts // 2
    
    for i in range(1, n_pts - 1):
        # Point-to-line distance
        x0, y0 = i, k_distances[i]
        
        numerator = abs((y2 - y1) * x0 - (x2 - x1) * y0 + x2 * y1 - y2 * x1)
        denominator = math.sqrt((y2 - y1) ** 2 + (x2 - x1) ** 2)
        
        if denominator > 0:
            dist = numerator / denominator
            if dist > max_dist:
                max_dist = dist
                elbow_idx = i
    
    return k_distances[elbow_idx], k_distances
```

## 7. Applications

### 7.1 Common Use Cases

- **Anomaly detection**: Noise points are outliers
- **Geographic clustering**: GPS trajectories, spatial data
- **Image segmentation**: Pixel clustering
- **Customer segmentation**: Behavioral clustering
- **Network intrusion detection**: Unusual traffic patterns
- **Social network analysis**: Community detection

### 7.2 Advantages

| Aspect | Benefit |
|--------|---------|
| Cluster shape | Can find arbitrary shapes |
| Noise handling | Automatic outlier detection |
| Number of clusters | No need to specify k |
| Robustness | Works well with noise |

### 7.3 Limitations

| Limitation | Impact |
|------------|--------|
| Varying density | May miss clusters |
| High dimensions | Curse of dimensionality |
| Parameter selection | eps and MinPts sensitive |

## 8. Real-World Software Engineering Applications

### 8.1 Production Example: GPS Trajectory Clustering

```python
from typing import List, Dict, Tuple, Optional, Set
import math
from datetime import datetime
from collections import defaultdict


class GPSTrajectoryDBSCAN:
    """
    Production DBSCAN for GPS trajectory clustering.
    
    Used for route analysis, stop detection, and location clustering.
    """
    
    # Earth radius in kilometers
    EARTH_RADIUS_KM = 6371.0
    
    def __init__(
        self,
        eps_meters: float = 50.0,
        min_samples: int = 3,
        min_stop_duration_seconds: int = 120
    ):
        """
        Initialize GPS trajectory clusterer.
        
        Args:
            eps_meters: Maximum distance for neighborhood (in meters)
            min_samples: Minimum points to form dense region
            min_stop_duration_seconds: Minimum time to consider a stop
        """
        self.eps_meters = eps_meters
        self.eps_km = eps_meters / 1000.0
        self.min_samples = min_samples
        self.min_stop_duration = min_stop_duration_seconds
        
        # Results
        self.stop_locations: List[Dict] = []
        self.route_segments: List[Dict] = []
        self.noise_points: List[Dict] = []
    
    def _haversine_distance(
        self,
        lat1: float,
        lon1: float,
        lat2: float,
        lon2: float
    ) -> float:
        """
        Calculate distance between GPS coordinates using Haversine formula.
        
        Returns distance in kilometers.
        """
        # Convert to radians
        lat1_rad = math.radians(lat1)
        lat2_rad = math.radians(lat2)
        delta_lat = math.radians(lat2 - lat1)
        delta_lon = math.radians(lon2 - lon1)
        
        a = (math.sin(delta_lat / 2) ** 2 +
             math.cos(lat1_rad) * math.cos(lat2_rad) *
             math.sin(delta_lon / 2) ** 2)
        c = 2 * math.atan2(math.sqrt(a), math.sqrt(1 - a))
        
        return self.EARTH_RADIUS_KM * c
    
    def _region_query(
        self,
        points: List[Dict],
        point_idx: int
    ) -> List[int]:
        """Find all points within eps distance."""
        neighbors = []
        p1 = points[point_idx]
        
        for i, p2 in enumerate(points):
            dist = self._haversine_distance(
                p1['latitude'], p1['longitude'],
                p2['latitude'], p2['longitude']
            )
            if dist <= self.eps_km:
                neighbors.append(i)
        
        return neighbors
    
    def _expand_cluster(
        self,
        points: List[Dict],
        point_idx: int,
        neighbors: List[int],
        cluster_id: int,
        labels: List[int]
    ):
        """Expand cluster from a core point."""
        labels[point_idx] = cluster_id
        
        seed_set = list(neighbors)
        i = 0
        
        while i < len(seed_set):
            current_idx = seed_set[i]
            
            if labels[current_idx] == -1:  # Noise
                labels[current_idx] = cluster_id
            
            if labels[current_idx] == -2:  # Undefined
                labels[current_idx] = cluster_id
                
                current_neighbors = self._region_query(points, current_idx)
                
                if len(current_neighbors) >= self.min_samples:
                    seed_set.extend(
                        n for n in current_neighbors
                        if n not in seed_set
                    )
            
            i += 1
    
    def cluster_trajectory(
        self,
        gps_points: List[Dict]
    ) -> Dict:
        """
        Cluster GPS trajectory points.
        
        Args:
            gps_points: List of {'latitude', 'longitude', 'timestamp', ...}
        
        Returns:
            Clustering results with stops and route segments.
        """
        if not gps_points:
            return {'stops': [], 'route_segments': [], 'noise': []}
        
        # Sort by timestamp
        points = sorted(gps_points, key=lambda p: p.get('timestamp', 0))
        
        n = len(points)
        labels = [-2] * n  # -2 = undefined, -1 = noise
        cluster_id = 0
        
        for i in range(n):
            if labels[i] != -2:
                continue
            
            neighbors = self._region_query(points, i)
            
            if len(neighbors) < self.min_samples:
                labels[i] = -1  # Noise
            else:
                self._expand_cluster(points, i, neighbors, cluster_id, labels)
                cluster_id += 1
        
        # Process clusters into stops and segments
        return self._process_clusters(points, labels, cluster_id)
    
    def _process_clusters(
        self,
        points: List[Dict],
        labels: List[int],
        n_clusters: int
    ) -> Dict:
        """Convert cluster labels to meaningful results."""
        stops = []
        route_segments = []
        noise_points = []
        
        # Group points by cluster
        clusters = defaultdict(list)
        for i, label in enumerate(labels):
            clusters[label].append(i)
        
        # Process each cluster
        for cluster_id in range(n_clusters):
            if cluster_id not in clusters:
                continue
            
            cluster_indices = clusters[cluster_id]
            cluster_points = [points[i] for i in cluster_indices]
            
            # Calculate stop location (centroid)
            avg_lat = sum(p['latitude'] for p in cluster_points) / len(cluster_points)
            avg_lon = sum(p['longitude'] for p in cluster_points) / len(cluster_points)
            
            # Calculate duration
            timestamps = [p.get('timestamp', 0) for p in cluster_points]
            if timestamps:
                duration = max(timestamps) - min(timestamps)
            else:
                duration = 0
            
            # Only consider as stop if duration exceeds threshold
            if duration >= self.min_stop_duration:
                stop = {
                    'cluster_id': cluster_id,
                    'centroid': {'latitude': avg_lat, 'longitude': avg_lon},
                    'n_points': len(cluster_points),
                    'duration_seconds': duration,
                    'arrival_time': min(timestamps) if timestamps else None,
                    'departure_time': max(timestamps) if timestamps else None,
                    'points': cluster_points
                }
                stops.append(stop)
        
        # Process noise points
        for idx in clusters.get(-1, []):
            noise_points.append(points[idx])
        
        # Identify route segments (transitions between stops)
        route_segments = self._identify_route_segments(points, labels, stops)
        
        self.stop_locations = stops
        self.route_segments = route_segments
        self.noise_points = noise_points
        
        return {
            'stops': stops,
            'route_segments': route_segments,
            'noise': noise_points,
            'n_clusters': n_clusters
        }
    
    def _identify_route_segments(
        self,
        points: List[Dict],
        labels: List[int],
        stops: List[Dict]
    ) -> List[Dict]:
        """Identify route segments between stops."""
        segments = []
        
        if len(stops) < 2:
            return segments
        
        # Sort stops by arrival time
        sorted_stops = sorted(stops, key=lambda s: s['arrival_time'] or 0)
        
        for i in range(len(sorted_stops) - 1):
            start_stop = sorted_stops[i]
            end_stop = sorted_stops[i + 1]
            
            # Find points between these stops (by timestamp)
            segment_points = []
            for j, point in enumerate(points):
                ts = point.get('timestamp', 0)
                if (start_stop['departure_time'] and 
                    end_stop['arrival_time'] and
                    start_stop['departure_time'] < ts < end_stop['arrival_time']):
                    segment_points.append(point)
            
            if segment_points:
                # Calculate segment statistics
                total_dist = 0
                for j in range(len(segment_points) - 1):
                    p1, p2 = segment_points[j], segment_points[j + 1]
                    total_dist += self._haversine_distance(
                        p1['latitude'], p1['longitude'],
                        p2['latitude'], p2['longitude']
                    )
                
                segment = {
                    'from_stop': start_stop['cluster_id'],
                    'to_stop': end_stop['cluster_id'],
                    'n_points': len(segment_points),
                    'distance_km': total_dist,
                    'duration_seconds': (
                        end_stop['arrival_time'] - start_stop['departure_time']
                    ),
                    'points': segment_points
                }
                segments.append(segment)
        
        return segments
    
    def detect_anomalous_stops(
        self,
        historical_stops: List[Dict]
    ) -> List[Dict]:
        """
        Detect anomalous stop locations based on historical patterns.
        
        Returns stops that don't match historical patterns.
        """
        if not self.stop_locations or not historical_stops:
            return []
        
        anomalous = []
        
        for stop in self.stop_locations:
            is_known = False
            
            for historical in historical_stops:
                dist = self._haversine_distance(
                    stop['centroid']['latitude'],
                    stop['centroid']['longitude'],
                    historical['latitude'],
                    historical['longitude']
                )
                
                if dist <= self.eps_km * 2:  # Within 2x epsilon
                    is_known = True
                    break
            
            if not is_known:
                anomalous.append(stop)
        
        return anomalous
    
    def get_summary(self) -> Dict:
        """Get clustering summary."""
        total_stop_duration = sum(
            s['duration_seconds'] for s in self.stop_locations
        )
        total_route_distance = sum(
            r['distance_km'] for r in self.route_segments
        )
        
        return {
            'n_stops': len(self.stop_locations),
            'n_route_segments': len(self.route_segments),
            'n_noise_points': len(self.noise_points),
            'total_stop_duration_hours': total_stop_duration / 3600,
            'total_route_distance_km': total_route_distance,
            'stop_locations': [
                {
                    'id': s['cluster_id'],
                    'lat': s['centroid']['latitude'],
                    'lon': s['centroid']['longitude'],
                    'duration_min': s['duration_seconds'] / 60
                }
                for s in self.stop_locations
            ]
        }


class AnomalyDetectorDBSCAN:
    """
    DBSCAN-based anomaly detection for general data.
    """
    
    def __init__(
        self,
        eps: float = 0.5,
        min_samples: int = 5,
        contamination: float = 0.1
    ):
        """
        Initialize anomaly detector.
        
        Args:
            eps: DBSCAN epsilon parameter
            min_samples: DBSCAN min_samples parameter
            contamination: Expected proportion of outliers
        """
        self.eps = eps
        self.min_samples = min_samples
        self.contamination = contamination
        
        self.dbscan = DBSCAN(eps=eps, min_samples=min_samples)
        self.threshold: float = 0.0
    
    def fit(self, X: List[List[float]]) -> 'AnomalyDetectorDBSCAN':
        """Fit the anomaly detector."""
        self.dbscan.fit(X)
        return self
    
    def predict(self, X: List[List[float]]) -> List[int]:
        """
        Predict anomalies.
        
        Returns:
            -1 for anomalies, 1 for normal points.
        """
        self.dbscan.fit(X)
        
        # Points labeled as noise are anomalies
        return [1 if label >= 0 else -1 for label in self.dbscan.labels_]
    
    def fit_predict(self, X: List[List[float]]) -> List[int]:
        """Fit and predict in one step."""
        return self.predict(X)
    
    def anomaly_scores(self, X: List[List[float]]) -> List[float]:
        """
        Compute anomaly scores (distance to nearest cluster).
        
        Higher score = more anomalous.
        """
        self.fit(X)
        
        # Find core points
        core_points = [X[i] for i in self.dbscan.core_sample_indices_]
        
        if not core_points:
            return [1.0] * len(X)
        
        scores = []
        for point in X:
            # Distance to nearest core point
            min_dist = float('inf')
            for core in core_points:
                dist = math.sqrt(sum((a - b) ** 2 for a, b in zip(point, core)))
                min_dist = min(min_dist, dist)
            
            # Normalize by eps
            scores.append(min_dist / self.eps)
        
        return scores


# Demo
if __name__ == "__main__":
    print("DBSCAN Demo")
    print("=" * 50)
    
    # Basic DBSCAN
    print("\n1. Basic Clustering:")
    
    # Generate clustered data with noise
    import random
    random.seed(42)
    
    X = []
    # Cluster 1: around (0, 0)
    for _ in range(20):
        X.append([random.gauss(0, 0.3), random.gauss(0, 0.3)])
    # Cluster 2: around (3, 3)
    for _ in range(20):
        X.append([random.gauss(3, 0.3), random.gauss(3, 0.3)])
    # Noise
    for _ in range(5):
        X.append([random.uniform(-2, 5), random.uniform(-2, 5)])
    
    dbscan = DBSCAN(eps=0.5, min_samples=3)
    labels = dbscan.fit_predict(X)
    
    print(f"   Number of clusters: {dbscan.n_clusters_}")
    print(f"   Number of core points: {len(dbscan.core_sample_indices_)}")
    print(f"   Noise points: {labels.count(-1)}")
    
    # Find optimal epsilon
    print("\n2. Parameter Selection:")
    eps, k_distances = find_optimal_eps(X, k=3)
    print(f"   Suggested epsilon: {eps:.3f}")
    
    # GPS trajectory example
    print("\n3. GPS Trajectory Clustering:")
    
    gps_data = [
        {'latitude': 40.7128, 'longitude': -74.0060, 'timestamp': 1000},
        {'latitude': 40.7129, 'longitude': -74.0061, 'timestamp': 1100},
        {'latitude': 40.7130, 'longitude': -74.0062, 'timestamp': 1200},
        {'latitude': 40.7580, 'longitude': -73.9855, 'timestamp': 2000},
        {'latitude': 40.7581, 'longitude': -73.9856, 'timestamp': 2100},
        {'latitude': 40.7582, 'longitude': -73.9857, 'timestamp': 2200},
    ]
    
    gps_clusterer = GPSTrajectoryDBSCAN(eps_meters=100, min_samples=2)
    result = gps_clusterer.cluster_trajectory(gps_data)
    
    print(f"   Stops found: {len(result['stops'])}")
    print(f"   Route segments: {len(result['route_segments'])}")
    
    # Anomaly detection
    print("\n4. Anomaly Detection:")
    
    detector = AnomalyDetectorDBSCAN(eps=0.5, min_samples=3)
    predictions = detector.fit_predict(X)
    
    anomalies = sum(1 for p in predictions if p == -1)
    print(f"   Anomalies detected: {anomalies}")
```

## 9. Comparison

| Algorithm | Shape | Noise | Params | Complexity |
|-----------|-------|-------|--------|------------|
| DBSCAN | Any | Yes | eps, MinPts | O(n²) / O(n log n) |
| K-Means | Spherical | No | k | O(nkd) |
| HDBSCAN | Any | Yes | min_samples | O(n²) |
| Mean Shift | Any | No | bandwidth | O(n²) |

## 10. Best Practices

1. **Scale features** before clustering (standardize)
2. **Use k-distance graph** to find epsilon
3. **Start with MinPts = 2×dimensions**
4. **Use spatial index** (KD-tree) for large datasets
5. **Handle varying density** with HDBSCAN or OPTICS
6. **Validate with silhouette score** or domain knowledge

## 11. References

- [Wikipedia: DBSCAN](https://en.wikipedia.org/wiki/DBSCAN)
- Ester, M. et al. "A Density-Based Algorithm for Discovering Clusters" (1996)
- Schubert, E. et al. "DBSCAN Revisited" (2017)
