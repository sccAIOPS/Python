# K-Means Clustering

## Overview
- **Category**: Unsupervised Learning - Clustering
- **Complexity**: Time: O(nkdi) | Space: O(nd + kd)
- **Type**: Partitional clustering
- **Source File**: [machine_learning/k_means_clust.py](../../../machine_learning/k_means_clust.py)

## 1. Mathematical Foundation

### 1.1 The Problem

Partition $n$ data points into $k$ clusters $C_1, \ldots, C_k$ to minimize within-cluster variance:

$$
\underset{C}{\text{argmin}} \sum_{i=1}^{k} \sum_{\mathbf{x} \in C_i} \|\mathbf{x} - \boldsymbol{\mu}_i\|^2
$$

where $\boldsymbol{\mu}_i$ is the centroid of cluster $C_i$:
$$
\boldsymbol{\mu}_i = \frac{1}{|C_i|} \sum_{\mathbf{x} \in C_i} \mathbf{x}
$$

### 1.2 The Algorithm (Lloyd's)

1. **Initialize**: Choose $k$ centroids randomly
2. **Assign**: Assign each point to nearest centroid
3. **Update**: Recompute centroids as cluster means
4. **Repeat**: Until convergence

### 1.3 Objective Function

**Inertia** (within-cluster sum of squares - WCSS):
$$
J = \sum_{i=1}^{k} \sum_{\mathbf{x} \in C_i} \|\mathbf{x} - \boldsymbol{\mu}_i\|^2
$$

K-means minimizes inertia but may converge to local minimum.

### 1.4 Convergence

K-means converges because:
- Assignment step reduces or maintains J
- Update step reduces or maintains J
- J is bounded below by 0
- Finite number of possible assignments

## 2. Initialization Methods

### 2.1 Random Initialization

Select $k$ random points as initial centroids.
- Simple but can lead to poor convergence

### 2.2 K-Means++ (Recommended)

1. Choose first centroid uniformly at random
2. For each subsequent centroid:
   - Compute $D(x)^2$ = distance to nearest existing centroid
   - Choose next centroid with probability $\propto D(x)^2$

**Benefit**: $O(\log k)$ competitive with optimal.

### 2.3 Multiple Restarts

Run k-means multiple times with different initializations, keep best result.

## 3. Pseudocode

```
ALGORITHM KMeans(X, k, max_iter)
    INPUT: Data X (n×d), number of clusters k, max iterations
    OUTPUT: Cluster assignments, centroids
    
    // Initialize centroids (K-Means++)
    centroids[0] ← random point from X
    for i ← 1 to k-1 do
        for each x in X do
            D[x] ← min distance to existing centroids
        
        centroids[i] ← sample x with probability ∝ D[x]^2
    
    // Main loop
    for iter ← 1 to max_iter do
        // Assignment step
        for each x in X do
            assignments[x] ← argmin_j ||x - centroids[j]||^2
        
        // Update step
        for j ← 0 to k-1 do
            cluster_j ← {x : assignments[x] = j}
            if cluster_j is not empty then
                centroids[j] ← mean(cluster_j)
        
        // Check convergence
        if assignments unchanged then
            break
    
    return assignments, centroids

ALGORITHM KMeansPlusPlus-Init(X, k)
    INPUT: Data X, number of clusters k
    OUTPUT: Initial centroids
    
    centroids ← [random choice from X]
    
    for i ← 1 to k-1 do
        distances ← []
        for each x in X do
            d ← min(||x - c||^2 for c in centroids)
            distances.append(d)
        
        // Sample proportional to squared distance
        probabilities ← distances / sum(distances)
        new_centroid ← weighted random choice from X with probabilities
        centroids.append(new_centroid)
    
    return centroids
```

## 4. Step-by-Step Example

### Example: Cluster points with k=2

**Data**: (1,1), (1,2), (2,1), (8,8), (8,9), (9,8)

**Iteration 1**:
- Random centroids: μ₁=(1,1), μ₂=(9,8)
- Assign:
  - (1,1) → μ₁ (d=0) vs μ₂ (d=10.6) → C₁
  - (1,2) → μ₁ (d=1) vs μ₂ (d=10.0) → C₁
  - (2,1) → μ₁ (d=1) vs μ₂ (d=9.9) → C₁
  - (8,8) → μ₁ (d=9.9) vs μ₂ (d=1) → C₂
  - (8,9) → μ₁ (d=10.6) vs μ₂ (d=1.4) → C₂
  - (9,8) → μ₁ (d=10.6) vs μ₂ (d=0) → C₂
- Update:
  - μ₁ = mean((1,1), (1,2), (2,1)) = (1.33, 1.33)
  - μ₂ = mean((8,8), (8,9), (9,8)) = (8.33, 8.33)

**Iteration 2**: Assignments unchanged → Converged!

## 5. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Random init | O(k) | O(kd) |
| K-means++ init | O(nkd) | O(kd) |
| Assignment | O(nkd) | O(n) |
| Update | O(nd) | O(kd) |
| Total (i iters) | O(nkdi) | O(nd + kd) |

### 5.1 Typically

- Converges in 10-300 iterations
- Linear in n, k, d per iteration
- May need multiple restarts

## 6. Visual Representation

### 6.1 K-Means Steps

```
Initial:                  After Assignment:         After Update:
    ●                         ●                         ●
  ●   ●     ★                 ●   ●     ★               ●   ●       ★
    ●                         ●                         ●       ↗
                                                              ★ (new)
      ○   ○                     ○   ○                     ○   ○
    ○       ○   ★               ○       ○   ★               ○       ○
      ○                           ○                    ↙      ○
                                                    ★ (new)

● = Cluster 1 points       ★ = Centroids
○ = Cluster 2 points
```

### 6.2 Elbow Method

```
Inertia │
        │\
        │ \
        │  \
        │   \_____
        │     ↑   \_____
        │   elbow       \____
        └─────────────────────── k
          1  2  3  4  5  6  7
```

## 7. Implementation

```python
from typing import List, Tuple, Optional
import random
import math


class KMeans:
    """
    K-Means clustering implementation.
    
    Supports K-Means++ initialization and multiple restarts.
    """
    
    def __init__(
        self,
        k: int = 3,
        max_iterations: int = 300,
        tolerance: float = 1e-4,
        n_init: int = 10,
        init_method: str = 'kmeans++'
    ):
        """
        Initialize K-Means.
        
        Args:
            k: Number of clusters
            max_iterations: Maximum iterations per run
            tolerance: Convergence threshold
            n_init: Number of initializations to try
            init_method: 'random' or 'kmeans++'
        """
        self.k = k
        self.max_iter = max_iterations
        self.tol = tolerance
        self.n_init = n_init
        self.init_method = init_method
        
        self.centroids: List[List[float]] = []
        self.labels: List[int] = []
        self.inertia: float = float('inf')
        self.n_iterations: int = 0
    
    def _distance(self, x: List[float], y: List[float]) -> float:
        """Euclidean distance squared."""
        return sum((a - b) ** 2 for a, b in zip(x, y))
    
    def _init_random(self, X: List[List[float]]) -> List[List[float]]:
        """Random initialization."""
        indices = random.sample(range(len(X)), self.k)
        return [X[i].copy() for i in indices]
    
    def _init_kmeans_plus_plus(
        self, 
        X: List[List[float]]
    ) -> List[List[float]]:
        """
        K-Means++ initialization.
        
        Provides better starting centroids.
        """
        n = len(X)
        
        # First centroid: random
        centroids = [X[random.randint(0, n - 1)].copy()]
        
        for _ in range(1, self.k):
            # Compute distances to nearest centroid
            distances = []
            for x in X:
                min_dist = min(self._distance(x, c) for c in centroids)
                distances.append(min_dist)
            
            # Sample proportional to D^2
            total = sum(distances)
            if total == 0:
                # All points are at centroids, pick random
                idx = random.randint(0, n - 1)
            else:
                r = random.random() * total
                cumsum = 0
                idx = 0
                for i, d in enumerate(distances):
                    cumsum += d
                    if cumsum >= r:
                        idx = i
                        break
            
            centroids.append(X[idx].copy())
        
        return centroids
    
    def _assign_clusters(
        self, 
        X: List[List[float]], 
        centroids: List[List[float]]
    ) -> List[int]:
        """Assign each point to nearest centroid."""
        labels = []
        for x in X:
            min_dist = float('inf')
            min_label = 0
            for j, c in enumerate(centroids):
                d = self._distance(x, c)
                if d < min_dist:
                    min_dist = d
                    min_label = j
            labels.append(min_label)
        return labels
    
    def _update_centroids(
        self, 
        X: List[List[float]], 
        labels: List[int]
    ) -> List[List[float]]:
        """Update centroids to cluster means."""
        d = len(X[0])
        centroids = [[0.0] * d for _ in range(self.k)]
        counts = [0] * self.k
        
        for x, label in zip(X, labels):
            for i in range(d):
                centroids[label][i] += x[i]
            counts[label] += 1
        
        for j in range(self.k):
            if counts[j] > 0:
                for i in range(d):
                    centroids[j][i] /= counts[j]
        
        return centroids
    
    def _compute_inertia(
        self, 
        X: List[List[float]], 
        labels: List[int],
        centroids: List[List[float]]
    ) -> float:
        """Compute within-cluster sum of squares."""
        return sum(
            self._distance(x, centroids[label])
            for x, label in zip(X, labels)
        )
    
    def _has_converged(
        self, 
        old_centroids: List[List[float]], 
        new_centroids: List[List[float]]
    ) -> bool:
        """Check if centroids have converged."""
        for old, new in zip(old_centroids, new_centroids):
            if self._distance(old, new) > self.tol:
                return False
        return True
    
    def _fit_single(
        self, 
        X: List[List[float]]
    ) -> Tuple[List[List[float]], List[int], float, int]:
        """Single run of K-Means."""
        # Initialize
        if self.init_method == 'kmeans++':
            centroids = self._init_kmeans_plus_plus(X)
        else:
            centroids = self._init_random(X)
        
        labels = []
        
        for iteration in range(self.max_iter):
            # Assign
            labels = self._assign_clusters(X, centroids)
            
            # Update
            new_centroids = self._update_centroids(X, labels)
            
            # Check convergence
            if self._has_converged(centroids, new_centroids):
                break
            
            centroids = new_centroids
        
        inertia = self._compute_inertia(X, labels, centroids)
        
        return centroids, labels, inertia, iteration + 1
    
    def fit(self, X: List[List[float]]) -> 'KMeans':
        """
        Fit K-Means to data.
        
        >>> km = KMeans(k=2, n_init=1)
        >>> X = [[0, 0], [0, 1], [1, 0], [10, 10], [10, 11], [11, 10]]
        >>> km.fit(X)  # doctest: +ELLIPSIS
        <...KMeans object at ...>
        >>> len(set(km.labels))
        2
        """
        best_inertia = float('inf')
        best_result = None
        
        for _ in range(self.n_init):
            centroids, labels, inertia, n_iter = self._fit_single(X)
            
            if inertia < best_inertia:
                best_inertia = inertia
                best_result = (centroids, labels, inertia, n_iter)
        
        self.centroids, self.labels, self.inertia, self.n_iterations = best_result
        
        return self
    
    def predict(self, X: List[List[float]]) -> List[int]:
        """
        Predict cluster labels for new data.
        
        >>> km = KMeans(k=2, n_init=1)
        >>> km.fit([[0, 0], [0, 1], [10, 10], [10, 11]])  # doctest: +ELLIPSIS
        <...>
        >>> labels = km.predict([[0.5, 0.5], [10.5, 10.5]])
        >>> labels[0] != labels[1]  # Different clusters
        True
        """
        return self._assign_clusters(X, self.centroids)
    
    def fit_predict(self, X: List[List[float]]) -> List[int]:
        """Fit and return labels."""
        self.fit(X)
        return self.labels
    
    def transform(self, X: List[List[float]]) -> List[List[float]]:
        """Transform to cluster-distance space."""
        result = []
        for x in X:
            distances = [
                math.sqrt(self._distance(x, c)) 
                for c in self.centroids
            ]
            result.append(distances)
        return result


def elbow_method(
    X: List[List[float]], 
    max_k: int = 10
) -> List[Tuple[int, float]]:
    """
    Compute inertia for different k values for elbow method.
    
    >>> X = [[0, 0], [0, 1], [1, 0], [10, 10], [10, 11], [11, 10]]
    >>> results = elbow_method(X, max_k=4)
    >>> len(results)
    4
    >>> results[1][1] < results[0][1]  # k=2 has lower inertia than k=1
    True
    """
    results = []
    
    for k in range(1, max_k + 1):
        km = KMeans(k=k, n_init=3)
        km.fit(X)
        results.append((k, km.inertia))
    
    return results


def silhouette_score(
    X: List[List[float]], 
    labels: List[int]
) -> float:
    """
    Compute mean silhouette score.
    
    s(i) = (b(i) - a(i)) / max(a(i), b(i))
    
    where:
    - a(i) = mean distance to points in same cluster
    - b(i) = mean distance to points in nearest other cluster
    
    >>> X = [[0, 0], [0, 1], [10, 10], [10, 11]]
    >>> labels = [0, 0, 1, 1]
    >>> score = silhouette_score(X, labels)
    >>> score > 0.9  # Well-separated clusters
    True
    """
    n = len(X)
    
    if n < 2:
        return 0.0
    
    unique_labels = set(labels)
    if len(unique_labels) == 1:
        return 0.0
    
    def distance(x1, x2):
        return math.sqrt(sum((a - b) ** 2 for a, b in zip(x1, x2)))
    
    silhouettes = []
    
    for i in range(n):
        # a(i): mean distance to same cluster
        same_cluster = [j for j in range(n) if labels[j] == labels[i] and j != i]
        if not same_cluster:
            a_i = 0
        else:
            a_i = sum(distance(X[i], X[j]) for j in same_cluster) / len(same_cluster)
        
        # b(i): min mean distance to other clusters
        b_i = float('inf')
        for label in unique_labels:
            if label == labels[i]:
                continue
            other_cluster = [j for j in range(n) if labels[j] == label]
            if other_cluster:
                mean_dist = sum(distance(X[i], X[j]) for j in other_cluster) / len(other_cluster)
                b_i = min(b_i, mean_dist)
        
        if b_i == float('inf'):
            b_i = 0
        
        # Silhouette
        if max(a_i, b_i) == 0:
            s_i = 0
        else:
            s_i = (b_i - a_i) / max(a_i, b_i)
        
        silhouettes.append(s_i)
    
    return sum(silhouettes) / len(silhouettes)


class MiniBatchKMeans:
    """
    Mini-batch K-Means for large datasets.
    
    Uses random subsets for faster updates.
    """
    
    def __init__(
        self,
        k: int = 3,
        batch_size: int = 100,
        max_iterations: int = 100
    ):
        self.k = k
        self.batch_size = batch_size
        self.max_iter = max_iterations
        self.centroids: List[List[float]] = []
        self.counts: List[int] = []
    
    def fit(self, X: List[List[float]]) -> 'MiniBatchKMeans':
        """
        Fit using mini-batches.
        
        >>> mbkm = MiniBatchKMeans(k=2, batch_size=3, max_iterations=10)
        >>> X = [[0, 0], [0, 1], [1, 0], [10, 10], [10, 11], [11, 10]]
        >>> mbkm.fit(X)  # doctest: +ELLIPSIS
        <...MiniBatchKMeans object at ...>
        """
        n = len(X)
        d = len(X[0])
        
        # Initialize with k-means++ on first batch
        first_batch = random.sample(X, min(self.batch_size, n))
        km = KMeans(k=self.k, n_init=1)
        km.fit(first_batch)
        
        self.centroids = km.centroids
        self.counts = [1] * self.k
        
        for _ in range(self.max_iter):
            # Sample batch
            batch = random.sample(X, min(self.batch_size, n))
            
            # Assign
            for x in batch:
                # Find nearest centroid
                min_dist = float('inf')
                nearest = 0
                for j, c in enumerate(self.centroids):
                    d = sum((a - b) ** 2 for a, b in zip(x, c))
                    if d < min_dist:
                        min_dist = d
                        nearest = j
                
                # Update with learning rate 1/count
                self.counts[nearest] += 1
                eta = 1.0 / self.counts[nearest]
                
                for i in range(len(x)):
                    self.centroids[nearest][i] += eta * (x[i] - self.centroids[nearest][i])
        
        return self
    
    def predict(self, X: List[List[float]]) -> List[int]:
        """Predict cluster labels."""
        labels = []
        for x in X:
            min_dist = float('inf')
            label = 0
            for j, c in enumerate(self.centroids):
                d = sum((a - b) ** 2 for a, b in zip(x, c))
                if d < min_dist:
                    min_dist = d
                    label = j
            labels.append(label)
        return labels
```

## 8. Applications

### 8.1 Classic Applications

- Customer segmentation
- Image compression (color quantization)
- Document clustering
- Anomaly detection (cluster outliers)
- Feature learning

### 8.2 Variants

| Variant | Description | Use Case |
|---------|-------------|----------|
| K-Medoids | Uses actual data points as centers | Outlier-robust |
| K-Modes | For categorical data | Non-numeric |
| Fuzzy C-Means | Soft assignments | Overlapping clusters |
| Spectral | Uses eigenvectors | Non-convex shapes |

## 9. Real-World Software Engineering Applications

### 9.1 Industry Use Cases

1. **Marketing**
   - Customer segmentation
   - Market basket analysis
   - Campaign targeting

2. **Image Processing**
   - Color quantization
   - Image segmentation
   - Feature extraction

3. **NLP**
   - Document clustering
   - Topic modeling
   - Word embeddings

### 9.2 Production Example: Customer Segmentation

```python
from typing import List, Dict, Tuple
import random


class CustomerSegmentation:
    """
    Customer segmentation using K-Means.
    """
    
    def __init__(self, n_segments: int = 5):
        self.n_segments = n_segments
        self.kmeans = KMeans(k=n_segments, n_init=10)
        self.feature_names: List[str] = []
        self.feature_stats: Dict[str, Tuple[float, float]] = {}
    
    def _normalize(
        self, 
        data: List[Dict[str, float]]
    ) -> List[List[float]]:
        """Normalize features to [0, 1] range."""
        if not data:
            return []
        
        self.feature_names = list(data[0].keys())
        
        # Compute min/max for each feature
        for name in self.feature_names:
            values = [d[name] for d in data]
            min_val = min(values)
            max_val = max(values)
            self.feature_stats[name] = (min_val, max_val)
        
        # Normalize
        normalized = []
        for d in data:
            row = []
            for name in self.feature_names:
                min_val, max_val = self.feature_stats[name]
                if max_val - min_val > 0:
                    row.append((d[name] - min_val) / (max_val - min_val))
                else:
                    row.append(0.5)
            normalized.append(row)
        
        return normalized
    
    def fit(self, customers: List[Dict[str, float]]) -> 'CustomerSegmentation':
        """
        Segment customers based on features.
        
        >>> seg = CustomerSegmentation(n_segments=2)
        >>> customers = [
        ...     {'spend': 100, 'frequency': 10},
        ...     {'spend': 120, 'frequency': 12},
        ...     {'spend': 1000, 'frequency': 2},
        ...     {'spend': 900, 'frequency': 1},
        ... ]
        >>> seg.fit(customers)  # doctest: +ELLIPSIS
        <...CustomerSegmentation object at ...>
        """
        X = self._normalize(customers)
        self.kmeans.fit(X)
        return self
    
    def predict(self, customers: List[Dict[str, float]]) -> List[int]:
        """Predict segment for new customers."""
        normalized = []
        for d in customers:
            row = []
            for name in self.feature_names:
                min_val, max_val = self.feature_stats[name]
                if max_val - min_val > 0:
                    row.append((d[name] - min_val) / (max_val - min_val))
                else:
                    row.append(0.5)
            normalized.append(row)
        
        return self.kmeans.predict(normalized)
    
    def get_segment_profiles(
        self, 
        customers: List[Dict[str, float]]
    ) -> List[Dict[str, float]]:
        """
        Get profile (mean features) for each segment.
        """
        labels = self.predict(customers)
        
        # Aggregate by segment
        segments: Dict[int, List[Dict[str, float]]] = {}
        for customer, label in zip(customers, labels):
            if label not in segments:
                segments[label] = []
            segments[label].append(customer)
        
        # Compute means
        profiles = []
        for seg_id in range(self.n_segments):
            if seg_id not in segments or not segments[seg_id]:
                profiles.append({name: 0 for name in self.feature_names})
                continue
            
            profile = {}
            for name in self.feature_names:
                values = [c[name] for c in segments[seg_id]]
                profile[name] = sum(values) / len(values)
            profile['count'] = len(segments[seg_id])
            profiles.append(profile)
        
        return profiles
    
    def describe_segments(
        self, 
        customers: List[Dict[str, float]]
    ) -> List[str]:
        """Generate human-readable segment descriptions."""
        profiles = self.get_segment_profiles(customers)
        
        descriptions = []
        for i, profile in enumerate(profiles):
            desc_parts = [f"Segment {i} ({profile.get('count', 0)} customers):"]
            
            for name in self.feature_names:
                value = profile.get(name, 0)
                min_val, max_val = self.feature_stats.get(name, (0, 1))
                
                # Categorize as low/medium/high
                range_val = max_val - min_val
                if range_val == 0:
                    level = "medium"
                elif (value - min_val) / range_val < 0.33:
                    level = "low"
                elif (value - min_val) / range_val < 0.67:
                    level = "medium"
                else:
                    level = "high"
                
                desc_parts.append(f"  - {name}: {level} ({value:.2f})")
            
            descriptions.append("\n".join(desc_parts))
        
        return descriptions


class ImageQuantizer:
    """
    Image color quantization using K-Means.
    
    Reduces number of colors in image for compression.
    """
    
    def __init__(self, n_colors: int = 16):
        self.n_colors = n_colors
        self.kmeans = KMeans(k=n_colors, n_init=3, max_iterations=50)
    
    def fit(self, pixels: List[List[int]]) -> 'ImageQuantizer':
        """
        Learn color palette from pixels.
        
        Args:
            pixels: List of [R, G, B] values (0-255)
        
        >>> quant = ImageQuantizer(n_colors=2)
        >>> pixels = [[255, 0, 0], [250, 5, 5], [0, 0, 255], [5, 5, 250]]
        >>> quant.fit(pixels)  # doctest: +ELLIPSIS
        <...ImageQuantizer object at ...>
        """
        # Convert to float [0, 1]
        X = [[p[0]/255, p[1]/255, p[2]/255] for p in pixels]
        self.kmeans.fit(X)
        return self
    
    def quantize(self, pixels: List[List[int]]) -> List[List[int]]:
        """
        Quantize pixel colors to learned palette.
        
        >>> quant = ImageQuantizer(n_colors=2)
        >>> pixels = [[255, 0, 0], [250, 5, 5], [0, 0, 255]]
        >>> quant.fit(pixels)  # doctest: +ELLIPSIS
        <...>
        >>> quantized = quant.quantize([[255, 0, 0]])
        >>> len(quantized)
        1
        """
        X = [[p[0]/255, p[1]/255, p[2]/255] for p in pixels]
        labels = self.kmeans.predict(X)
        
        result = []
        for label in labels:
            c = self.kmeans.centroids[label]
            result.append([
                int(c[0] * 255),
                int(c[1] * 255),
                int(c[2] * 255)
            ])
        
        return result
    
    def get_palette(self) -> List[List[int]]:
        """Get the learned color palette."""
        return [
            [int(c[0]*255), int(c[1]*255), int(c[2]*255)]
            for c in self.kmeans.centroids
        ]
    
    def compression_ratio(self, width: int, height: int) -> float:
        """
        Calculate theoretical compression ratio.
        
        Original: 24 bits per pixel (RGB)
        Quantized: log2(n_colors) bits per pixel + palette
        """
        original_bits = width * height * 24
        
        bits_per_pixel = math.ceil(math.log2(self.n_colors))
        quantized_bits = width * height * bits_per_pixel + self.n_colors * 24
        
        return original_bits / quantized_bits


# Demo
if __name__ == "__main__":
    print("Customer Segmentation Demo")
    print("=" * 40)
    
    # Generate synthetic customer data
    random.seed(42)
    customers = []
    
    # High-value, frequent
    for _ in range(30):
        customers.append({
            'annual_spend': random.gauss(5000, 500),
            'frequency': random.gauss(50, 10),
            'recency_days': random.gauss(5, 2)
        })
    
    # Low-value, occasional
    for _ in range(40):
        customers.append({
            'annual_spend': random.gauss(200, 50),
            'frequency': random.gauss(5, 2),
            'recency_days': random.gauss(60, 20)
        })
    
    # Medium-value, regular
    for _ in range(30):
        customers.append({
            'annual_spend': random.gauss(1500, 300),
            'frequency': random.gauss(20, 5),
            'recency_days': random.gauss(20, 5)
        })
    
    # Segment
    seg = CustomerSegmentation(n_segments=3)
    seg.fit(customers)
    
    descriptions = seg.describe_segments(customers)
    for desc in descriptions:
        print(desc)
        print()
    
    print("\nImage Quantization Demo")
    print("=" * 40)
    
    # Synthetic image pixels
    pixels = []
    for _ in range(100):
        pixels.append([random.randint(200, 255), random.randint(0, 50), random.randint(0, 50)])  # Red
    for _ in range(100):
        pixels.append([random.randint(0, 50), random.randint(200, 255), random.randint(0, 50)])  # Green
    for _ in range(100):
        pixels.append([random.randint(0, 50), random.randint(0, 50), random.randint(200, 255)])  # Blue
    
    quant = ImageQuantizer(n_colors=4)
    quant.fit(pixels)
    
    palette = quant.get_palette()
    print(f"Learned palette ({len(palette)} colors):")
    for i, color in enumerate(palette):
        print(f"  Color {i}: RGB{tuple(color)}")
    
    compression = quant.compression_ratio(100, 100)
    print(f"\nTheoretical compression ratio: {compression:.1f}x")
```

## 10. Choosing k

### 10.1 Elbow Method

Plot inertia vs k, look for "elbow" point.

### 10.2 Silhouette Method

Choose k that maximizes mean silhouette score.

### 10.3 Gap Statistic

Compare inertia to expected under null (uniform) distribution.

### 10.4 Domain Knowledge

Often the number of clusters is known from the problem context.

## 11. Limitations and Solutions

| Limitation | Solution |
|------------|----------|
| Spherical clusters only | Use spectral clustering |
| Sensitive to outliers | Use K-medoids or remove outliers |
| Requires k | Use elbow/silhouette/gap methods |
| Local optima | Multiple restarts, K-means++ |
| Scales poorly | Mini-batch K-means |

## 12. References

- [Wikipedia: K-Means Clustering](https://en.wikipedia.org/wiki/K-means_clustering)
- Arthur, D. and Vassilvitskii, S. "k-means++: The Advantages of Careful Seeding" (2007)
- MacQueen, J. "Some Methods for classification and Analysis of Multivariate Observations" (1967)
