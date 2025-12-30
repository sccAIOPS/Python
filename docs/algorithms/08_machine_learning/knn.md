# K-Nearest Neighbors (KNN)

## Overview
- **Category**: Supervised Learning - Classification/Regression
- **Complexity**: Training: O(1) | Prediction: O(nd)
- **Type**: Instance-based (lazy) learning
- **Source File**: [machine_learning/k_nearest_neighbours.py](../../../machine_learning/k_nearest_neighbours.py)

## 1. Mathematical Foundation

### 1.1 The Algorithm

For a query point $\mathbf{x}_q$:
1. Compute distance to all training points
2. Find $k$ nearest neighbors
3. Classify by majority vote (classification) or average (regression)

### 1.2 Distance Metrics

**Euclidean Distance (L2)**:
$$
d(\mathbf{x}, \mathbf{y}) = \sqrt{\sum_{i=1}^{d} (x_i - y_i)^2}
$$

**Manhattan Distance (L1)**:
$$
d(\mathbf{x}, \mathbf{y}) = \sum_{i=1}^{d} |x_i - y_i|
$$

**Minkowski Distance (Lp)**:
$$
d(\mathbf{x}, \mathbf{y}) = \left(\sum_{i=1}^{d} |x_i - y_i|^p\right)^{1/p}
$$

**Cosine Similarity**:
$$
\text{sim}(\mathbf{x}, \mathbf{y}) = \frac{\mathbf{x} \cdot \mathbf{y}}{\|\mathbf{x}\| \|\mathbf{y}\|}
$$

### 1.3 Classification Rule

$$
\hat{y} = \underset{c}{\text{argmax}} \sum_{i \in N_k(\mathbf{x})} \mathbb{1}(y_i = c)
$$

where $N_k(\mathbf{x})$ is the set of $k$ nearest neighbors.

### 1.4 Weighted KNN

Weight votes by inverse distance:
$$
\hat{y} = \underset{c}{\text{argmax}} \sum_{i \in N_k(\mathbf{x})} \frac{1}{d(\mathbf{x}, \mathbf{x}_i)^2} \mathbb{1}(y_i = c)
$$

### 1.5 Regression Variant

$$
\hat{y} = \frac{1}{k} \sum_{i \in N_k(\mathbf{x})} y_i
$$

Or weighted:
$$
\hat{y} = \frac{\sum_{i \in N_k(\mathbf{x})} w_i y_i}{\sum_{i \in N_k(\mathbf{x})} w_i}
$$

## 2. Choosing k

### 2.1 Effect of k

| k | Pros | Cons |
|---|------|------|
| Small (1-3) | Flexible boundaries | Noisy, overfitting |
| Large | Smooth boundaries | Underfitting, slow |
| √n (rule of thumb) | Balanced | May not be optimal |

### 2.2 Odd vs Even k

Use **odd k** for binary classification to avoid ties.

### 2.3 Cross-Validation

Find optimal k by testing various values and selecting based on validation accuracy.

## 3. Pseudocode

```
ALGORITHM KNN-Classify(training_data, query_point, k)
    INPUT: Training set D = {(x_i, y_i)}, query x_q, number of neighbors k
    OUTPUT: Predicted class for x_q
    
    distances ← []
    for each (x_i, y_i) in D do
        d ← Distance(x_q, x_i)
        distances.append((d, y_i))
    
    // Find k nearest
    Sort distances by d
    neighbors ← distances[0:k]
    
    // Majority vote
    class_counts ← {}
    for (d, y) in neighbors do
        class_counts[y] ← class_counts[y] + 1
    
    return argmax(class_counts)

ALGORITHM KNN-Regression(training_data, query_point, k)
    INPUT: Training set D = {(x_i, y_i)}, query x_q, number of neighbors k
    OUTPUT: Predicted value for x_q
    
    distances ← []
    for each (x_i, y_i) in D do
        d ← Distance(x_q, x_i)
        distances.append((d, y_i))
    
    Sort distances by d
    neighbors ← distances[0:k]
    
    // Average
    return sum(y for (d, y) in neighbors) / k

ALGORITHM KNN-WeightedClassify(training_data, query_point, k)
    INPUT: Training set D, query x_q, k
    OUTPUT: Predicted class
    
    distances ← []
    for each (x_i, y_i) in D do
        d ← Distance(x_q, x_i)
        distances.append((d, y_i))
    
    Sort distances by d
    neighbors ← distances[0:k]
    
    // Weighted vote
    class_weights ← {}
    for (d, y) in neighbors do
        weight ← 1 / (d^2 + epsilon)  // epsilon to avoid div by 0
        class_weights[y] ← class_weights[y] + weight
    
    return argmax(class_weights)
```

## 4. Step-by-Step Example

### Example: Classify point (5, 5) with k=3

**Training data**:
| Point | Class |
|-------|-------|
| (1, 1) | A |
| (2, 1) | A |
| (4, 4) | B |
| (6, 5) | B |
| (7, 7) | B |

**Distances to (5, 5)**:
- d(5,5), (1,1)) = √[(5-1)² + (5-1)²] = √32 ≈ 5.66 → A
- d((5,5), (2,1)) = √[(5-2)² + (5-1)²] = √25 = 5.00 → A
- d((5,5), (4,4)) = √[(5-4)² + (5-4)²] = √2 ≈ 1.41 → B
- d((5,5), (6,5)) = √[(5-6)² + (5-5)²] = 1.00 → B
- d((5,5), (7,7)) = √[(5-7)² + (5-7)²] = √8 ≈ 2.83 → B

**3 nearest**: (6,5)→B, (4,4)→B, (7,7)→B

**Prediction**: B (3/3 = 100%)

## 5. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Training | O(1) | O(nd) |
| Prediction (brute) | O(nd) | O(n) |
| Prediction (KD-tree) | O(d log n) average | O(nd) |
| Prediction (Ball tree) | O(d log n) average | O(nd) |

### 5.1 Curse of Dimensionality

In high dimensions (d >> 10):
- Points become equidistant
- KD-trees degrade to O(n)
- Need dimensionality reduction

## 6. Visual Representation

### 6.1 Decision Boundary (k=1)

```
    ●               ○
  ● ● ●           ○ ○
    ●     ════════    ○
        ║        ║
  ●     ║   ?    ║    ○
        ║        ║
    ● ══╝        ╚════ ○
  ●                   ○ ○
  
● = Class A    ○ = Class B    ? = Query
════ = Decision boundary
```

### 6.2 Effect of k

```
k=1: Irregular          k=5: Smoother          k=n: Most common class
    ┌──┬──┐                ┌─────┐                 ┌─────────┐
    │A │B │                │  A  │                 │         │
    ├──┴──┤                │     │                 │    A    │
    │  A  │                │  A  │                 │         │
    └─────┘                └─────┘                 └─────────┘
```

### 6.3 Weighted vs Unweighted

```
Query ★ equidistant from 2 A's and 1 B

Unweighted (k=3):         Weighted by distance:
  A ●───┐                   A ●───┐ (d=1, w=1)
        │                         │
  A ●───★                   A ●───★ (d=1, w=1)
        │                         │
  B ○───┘                   B ○───┘ (d=1, w=1)
  
  Vote: A=2, B=1            Weighted: same
  Predict: A                Predict: A
```

## 7. Implementation

```python
from typing import List, Tuple, Dict, Optional, Callable
from collections import Counter
import math
import heapq


class KNearestNeighbors:
    """
    K-Nearest Neighbors classifier/regressor.
    
    Supports multiple distance metrics and weighted voting.
    """
    
    def __init__(
        self,
        k: int = 5,
        metric: str = 'euclidean',
        weighted: bool = False,
        task: str = 'classification'
    ):
        """
        Initialize KNN model.
        
        Args:
            k: Number of neighbors
            metric: 'euclidean', 'manhattan', 'cosine', 'minkowski'
            weighted: Whether to weight votes by inverse distance
            task: 'classification' or 'regression'
        """
        self.k = k
        self.metric = metric
        self.weighted = weighted
        self.task = task
        
        self.X_train: List[List[float]] = []
        self.y_train: List = []
    
    def _euclidean(self, x1: List[float], x2: List[float]) -> float:
        """Euclidean (L2) distance."""
        return math.sqrt(sum((a - b) ** 2 for a, b in zip(x1, x2)))
    
    def _manhattan(self, x1: List[float], x2: List[float]) -> float:
        """Manhattan (L1) distance."""
        return sum(abs(a - b) for a, b in zip(x1, x2))
    
    def _cosine(self, x1: List[float], x2: List[float]) -> float:
        """Cosine distance (1 - similarity)."""
        dot = sum(a * b for a, b in zip(x1, x2))
        norm1 = math.sqrt(sum(a ** 2 for a in x1))
        norm2 = math.sqrt(sum(b ** 2 for b in x2))
        
        if norm1 == 0 or norm2 == 0:
            return 1.0
        
        return 1 - dot / (norm1 * norm2)
    
    def _minkowski(self, x1: List[float], x2: List[float], p: int = 3) -> float:
        """Minkowski (Lp) distance."""
        return sum(abs(a - b) ** p for a, b in zip(x1, x2)) ** (1/p)
    
    def _distance(self, x1: List[float], x2: List[float]) -> float:
        """Compute distance based on selected metric."""
        if self.metric == 'euclidean':
            return self._euclidean(x1, x2)
        elif self.metric == 'manhattan':
            return self._manhattan(x1, x2)
        elif self.metric == 'cosine':
            return self._cosine(x1, x2)
        elif self.metric == 'minkowski':
            return self._minkowski(x1, x2)
        else:
            return self._euclidean(x1, x2)
    
    def fit(self, X: List[List[float]], y: List) -> 'KNearestNeighbors':
        """
        Store training data (lazy learning - no actual training).
        
        >>> knn = KNearestNeighbors(k=3)
        >>> knn.fit([[0, 0], [1, 1], [2, 2]], [0, 0, 1])  # doctest: +ELLIPSIS
        <...KNearestNeighbors object at ...>
        """
        self.X_train = X
        self.y_train = y
        return self
    
    def _find_neighbors(self, x: List[float]) -> List[Tuple[float, int]]:
        """Find k nearest neighbors using a heap for efficiency."""
        # Use negative distance for max-heap behavior
        heap = []
        
        for i, x_train in enumerate(self.X_train):
            dist = self._distance(x, x_train)
            
            if len(heap) < self.k:
                heapq.heappush(heap, (-dist, i))
            elif dist < -heap[0][0]:
                heapq.heapreplace(heap, (-dist, i))
        
        # Return (distance, index) pairs sorted by distance
        neighbors = [(-d, i) for d, i in heap]
        neighbors.sort()
        return neighbors
    
    def _classify(self, neighbors: List[Tuple[float, int]]) -> int:
        """Classify by (weighted) majority vote."""
        if self.weighted:
            class_weights: Dict = {}
            for dist, idx in neighbors:
                label = self.y_train[idx]
                weight = 1.0 / (dist ** 2 + 1e-10)
                class_weights[label] = class_weights.get(label, 0) + weight
            
            return max(class_weights, key=class_weights.get)
        else:
            labels = [self.y_train[idx] for _, idx in neighbors]
            return Counter(labels).most_common(1)[0][0]
    
    def _regress(self, neighbors: List[Tuple[float, int]]) -> float:
        """Regress by (weighted) average."""
        if self.weighted:
            weighted_sum = 0
            weight_total = 0
            for dist, idx in neighbors:
                weight = 1.0 / (dist ** 2 + 1e-10)
                weighted_sum += weight * self.y_train[idx]
                weight_total += weight
            return weighted_sum / weight_total
        else:
            return sum(self.y_train[idx] for _, idx in neighbors) / len(neighbors)
    
    def predict_single(self, x: List[float]):
        """
        Predict for a single sample.
        
        >>> knn = KNearestNeighbors(k=3)
        >>> knn.fit([[0, 0], [0, 1], [1, 0], [5, 5], [5, 6], [6, 5]], [0, 0, 0, 1, 1, 1])
        ... # doctest: +ELLIPSIS
        <...>
        >>> knn.predict_single([0.5, 0.5])
        0
        >>> knn.predict_single([5.5, 5.5])
        1
        """
        neighbors = self._find_neighbors(x)
        
        if self.task == 'classification':
            return self._classify(neighbors)
        else:
            return self._regress(neighbors)
    
    def predict(self, X: List[List[float]]) -> List:
        """Predict for multiple samples."""
        return [self.predict_single(x) for x in X]
    
    def score(self, X: List[List[float]], y: List) -> float:
        """
        Compute accuracy (classification) or R² (regression).
        """
        predictions = self.predict(X)
        
        if self.task == 'classification':
            correct = sum(1 for p, t in zip(predictions, y) if p == t)
            return correct / len(y)
        else:
            y_mean = sum(y) / len(y)
            ss_res = sum((t - p) ** 2 for t, p in zip(y, predictions))
            ss_tot = sum((t - y_mean) ** 2 for t in y)
            return 1 - ss_res / ss_tot if ss_tot > 0 else 0


class KDTree:
    """
    KD-Tree for efficient nearest neighbor search.
    
    Average case: O(log n) for query
    Worst case: O(n) in high dimensions
    """
    
    def __init__(self, points: List[List[float]], labels: List = None):
        self.k = len(points[0]) if points else 0
        self.labels = labels
        self.root = self._build(list(range(len(points))), points, 0)
    
    def _build(
        self, 
        indices: List[int], 
        points: List[List[float]], 
        depth: int
    ) -> Optional[Dict]:
        """Recursively build KD-tree."""
        if not indices:
            return None
        
        axis = depth % self.k
        
        # Sort by current axis
        indices.sort(key=lambda i: points[i][axis])
        mid = len(indices) // 2
        
        return {
            'point': points[indices[mid]],
            'index': indices[mid],
            'left': self._build(indices[:mid], points, depth + 1),
            'right': self._build(indices[mid + 1:], points, depth + 1)
        }
    
    def _search(
        self,
        node: Optional[Dict],
        query: List[float],
        k: int,
        depth: int,
        best: List[Tuple[float, int]]
    ):
        """Search for k nearest neighbors."""
        if node is None:
            return
        
        point = node['point']
        dist = math.sqrt(sum((a - b) ** 2 for a, b in zip(query, point)))
        
        # Update best
        if len(best) < k:
            heapq.heappush(best, (-dist, node['index']))
        elif dist < -best[0][0]:
            heapq.heapreplace(best, (-dist, node['index']))
        
        axis = depth % self.k
        diff = query[axis] - point[axis]
        
        # Search closer side first
        close = node['left'] if diff < 0 else node['right']
        far = node['right'] if diff < 0 else node['left']
        
        self._search(close, query, k, depth + 1, best)
        
        # Check if we need to search far side
        if len(best) < k or abs(diff) < -best[0][0]:
            self._search(far, query, k, depth + 1, best)
    
    def query(self, point: List[float], k: int) -> List[Tuple[float, int]]:
        """
        Find k nearest neighbors.
        
        Returns list of (distance, index) pairs.
        """
        best = []
        self._search(self.root, point, k, 0, best)
        result = [(-d, i) for d, i in best]
        result.sort()
        return result


class KNNWithKDTree:
    """
    KNN using KD-Tree for faster queries.
    """
    
    def __init__(self, k: int = 5, task: str = 'classification'):
        self.k = k
        self.task = task
        self.tree: Optional[KDTree] = None
        self.y_train: List = []
    
    def fit(self, X: List[List[float]], y: List) -> 'KNNWithKDTree':
        """Build KD-tree from training data."""
        self.tree = KDTree(X, y)
        self.y_train = y
        return self
    
    def predict_single(self, x: List[float]):
        """Predict using KD-tree search."""
        neighbors = self.tree.query(x, self.k)
        
        if self.task == 'classification':
            labels = [self.y_train[idx] for _, idx in neighbors]
            return Counter(labels).most_common(1)[0][0]
        else:
            return sum(self.y_train[idx] for _, idx in neighbors) / self.k
    
    def predict(self, X: List[List[float]]) -> List:
        """Predict for multiple samples."""
        return [self.predict_single(x) for x in X]
```

## 8. Applications

### 8.1 Classic Applications

- Handwritten digit recognition
- Recommendation systems
- Medical diagnosis
- Anomaly detection
- Text classification

### 8.2 Variations

- **Radius-based**: Use all neighbors within radius r
- **Prototype-based**: Reduce training set to representative points
- **Metric learning**: Learn optimal distance function

## 9. Real-World Software Engineering Applications

### 9.1 Industry Use Cases

1. **Recommendation Systems**
   - Similar item recommendations
   - Collaborative filtering
   - Content-based filtering

2. **Fraud Detection**
   - Identify unusual transactions
   - Compare to known fraud patterns

3. **Image Recognition**
   - Face recognition
   - Object classification
   - Image retrieval

### 9.2 Production Example: Recommendation Engine

```python
from typing import List, Dict, Tuple, Set
import math


class ContentBasedRecommender:
    """
    Content-based recommendation using KNN.
    
    Recommends items similar to user's liked items.
    """
    
    def __init__(self, k: int = 10):
        self.k = k
        self.items: Dict[int, List[float]] = {}  # item_id -> features
        self.item_names: Dict[int, str] = {}
    
    def add_item(
        self, 
        item_id: int, 
        features: List[float], 
        name: str = ""
    ):
        """Add item with feature vector."""
        self.items[item_id] = features
        self.item_names[item_id] = name
    
    def _cosine_similarity(
        self, 
        v1: List[float], 
        v2: List[float]
    ) -> float:
        """Compute cosine similarity between vectors."""
        dot = sum(a * b for a, b in zip(v1, v2))
        norm1 = math.sqrt(sum(a ** 2 for a in v1))
        norm2 = math.sqrt(sum(b ** 2 for b in v2))
        
        if norm1 == 0 or norm2 == 0:
            return 0.0
        
        return dot / (norm1 * norm2)
    
    def find_similar(
        self, 
        item_id: int, 
        exclude: Set[int] = None
    ) -> List[Tuple[int, float]]:
        """
        Find k most similar items to given item.
        
        >>> rec = ContentBasedRecommender(k=2)
        >>> rec.add_item(1, [1, 0, 0], "Action Movie")
        >>> rec.add_item(2, [0.9, 0.1, 0], "Action Movie 2")
        >>> rec.add_item(3, [0, 1, 0], "Romance")
        >>> similar = rec.find_similar(1)
        >>> similar[0][0]  # Most similar to item 1
        2
        """
        if item_id not in self.items:
            return []
        
        exclude = exclude or set()
        exclude.add(item_id)
        
        query_features = self.items[item_id]
        similarities = []
        
        for other_id, features in self.items.items():
            if other_id in exclude:
                continue
            
            sim = self._cosine_similarity(query_features, features)
            similarities.append((other_id, sim))
        
        # Sort by similarity descending
        similarities.sort(key=lambda x: -x[1])
        
        return similarities[:self.k]
    
    def recommend_for_user(
        self,
        liked_items: List[int],
        n_recommendations: int = 5
    ) -> List[Tuple[int, float, str]]:
        """
        Recommend items based on user's liked items.
        
        >>> rec = ContentBasedRecommender(k=5)
        >>> # Add movies with genre features [action, romance, comedy]
        >>> rec.add_item(1, [1, 0, 0], "Die Hard")
        >>> rec.add_item(2, [0.9, 0, 0.1], "Mad Max")
        >>> rec.add_item(3, [0, 1, 0], "Titanic")
        >>> rec.add_item(4, [0, 0.9, 0.1], "The Notebook")
        >>> rec.add_item(5, [0.5, 0.5, 0], "Mr & Mrs Smith")
        >>> recs = rec.recommend_for_user([1])  # User likes Die Hard
        >>> recs[0][2]  # Top recommendation
        'Mad Max'
        """
        liked_set = set(liked_items)
        
        # Aggregate scores from all liked items
        item_scores: Dict[int, float] = {}
        
        for liked_id in liked_items:
            similar = self.find_similar(liked_id, exclude=liked_set)
            
            for item_id, sim in similar:
                if item_id in liked_set:
                    continue
                
                if item_id not in item_scores:
                    item_scores[item_id] = 0
                item_scores[item_id] += sim
        
        # Sort by score
        recommendations = sorted(
            item_scores.items(), 
            key=lambda x: -x[1]
        )[:n_recommendations]
        
        return [
            (item_id, score, self.item_names.get(item_id, ""))
            for item_id, score in recommendations
        ]


class CollaborativeFilteringKNN:
    """
    User-based collaborative filtering using KNN.
    
    Recommends items liked by similar users.
    """
    
    def __init__(self, k: int = 10):
        self.k = k
        self.user_ratings: Dict[int, Dict[int, float]] = {}  # user -> {item: rating}
    
    def add_rating(self, user_id: int, item_id: int, rating: float):
        """Add a user rating for an item."""
        if user_id not in self.user_ratings:
            self.user_ratings[user_id] = {}
        self.user_ratings[user_id][item_id] = rating
    
    def _user_similarity(self, user1: int, user2: int) -> float:
        """
        Compute Pearson correlation between two users.
        """
        ratings1 = self.user_ratings.get(user1, {})
        ratings2 = self.user_ratings.get(user2, {})
        
        # Find common items
        common = set(ratings1.keys()) & set(ratings2.keys())
        
        if len(common) < 2:
            return 0.0
        
        # Compute means
        mean1 = sum(ratings1[i] for i in common) / len(common)
        mean2 = sum(ratings2[i] for i in common) / len(common)
        
        # Pearson correlation
        numerator = sum(
            (ratings1[i] - mean1) * (ratings2[i] - mean2) 
            for i in common
        )
        
        denom1 = math.sqrt(sum((ratings1[i] - mean1) ** 2 for i in common))
        denom2 = math.sqrt(sum((ratings2[i] - mean2) ** 2 for i in common))
        
        if denom1 == 0 or denom2 == 0:
            return 0.0
        
        return numerator / (denom1 * denom2)
    
    def find_similar_users(self, user_id: int) -> List[Tuple[int, float]]:
        """Find k most similar users."""
        similarities = []
        
        for other_id in self.user_ratings:
            if other_id == user_id:
                continue
            
            sim = self._user_similarity(user_id, other_id)
            if sim > 0:  # Only consider positive correlations
                similarities.append((other_id, sim))
        
        similarities.sort(key=lambda x: -x[1])
        return similarities[:self.k]
    
    def predict_rating(self, user_id: int, item_id: int) -> float:
        """
        Predict user's rating for an item.
        
        Uses weighted average of similar users' ratings.
        """
        if user_id not in self.user_ratings:
            return 3.0  # Default rating
        
        user_ratings = self.user_ratings[user_id]
        user_mean = sum(user_ratings.values()) / len(user_ratings)
        
        similar_users = self.find_similar_users(user_id)
        
        numerator = 0
        denominator = 0
        
        for other_id, similarity in similar_users:
            if item_id not in self.user_ratings[other_id]:
                continue
            
            other_ratings = self.user_ratings[other_id]
            other_mean = sum(other_ratings.values()) / len(other_ratings)
            
            numerator += similarity * (other_ratings[item_id] - other_mean)
            denominator += abs(similarity)
        
        if denominator == 0:
            return user_mean
        
        return user_mean + numerator / denominator
    
    def recommend(
        self, 
        user_id: int, 
        n: int = 5
    ) -> List[Tuple[int, float]]:
        """
        Recommend top n items for user.
        """
        user_items = set(self.user_ratings.get(user_id, {}).keys())
        
        # Get all items rated by similar users
        candidate_items = set()
        for other_id, _ in self.find_similar_users(user_id):
            candidate_items.update(self.user_ratings[other_id].keys())
        
        candidate_items -= user_items
        
        # Predict ratings for candidates
        predictions = []
        for item_id in candidate_items:
            pred = self.predict_rating(user_id, item_id)
            predictions.append((item_id, pred))
        
        predictions.sort(key=lambda x: -x[1])
        return predictions[:n]


# Demo
if __name__ == "__main__":
    print("Content-Based Recommendation Demo")
    print("=" * 40)
    
    recommender = ContentBasedRecommender(k=3)
    
    # Add movies with feature vectors [action, comedy, drama, scifi]
    movies = [
        (1, [0.9, 0.1, 0.0, 0.0], "Die Hard"),
        (2, [0.8, 0.2, 0.0, 0.0], "Lethal Weapon"),
        (3, [0.1, 0.9, 0.0, 0.0], "Home Alone"),
        (4, [0.0, 0.8, 0.2, 0.0], "Mrs. Doubtfire"),
        (5, [0.0, 0.1, 0.9, 0.0], "The Shawshank Redemption"),
        (6, [0.0, 0.0, 0.8, 0.2], "Forrest Gump"),
        (7, [0.5, 0.0, 0.0, 0.5], "The Matrix"),
        (8, [0.3, 0.0, 0.0, 0.7], "Inception"),
    ]
    
    for mid, features, name in movies:
        recommender.add_item(mid, features, name)
    
    # User likes action movies
    liked = [1, 2]  # Die Hard, Lethal Weapon
    recommendations = recommender.recommend_for_user(liked, n_recommendations=3)
    
    print(f"User liked: {[movies[i-1][2] for i in liked]}")
    print("\nRecommendations:")
    for item_id, score, name in recommendations:
        print(f"  {name} (score: {score:.3f})")
```

## 10. Hyperparameter Tuning

### 10.1 Parameters to Tune

| Parameter | Range | Impact |
|-----------|-------|--------|
| k | 1 to √n | Bias-variance tradeoff |
| metric | euclidean, manhattan, etc. | Distance perception |
| weights | uniform, distance | Vote importance |

### 10.2 Grid Search Example

```python
def grid_search_knn(X_train, y_train, X_val, y_val):
    best_k = 1
    best_metric = 'euclidean'
    best_score = 0
    
    for k in [1, 3, 5, 7, 9, 11, 15, 21]:
        for metric in ['euclidean', 'manhattan', 'cosine']:
            knn = KNearestNeighbors(k=k, metric=metric)
            knn.fit(X_train, y_train)
            score = knn.score(X_val, y_val)
            
            if score > best_score:
                best_score = score
                best_k = k
                best_metric = metric
    
    return best_k, best_metric, best_score
```

## 11. Edge Cases

| Scenario | Solution |
|----------|----------|
| Ties in voting | Use weighted voting or random |
| k > n | Use k = n |
| Imbalanced classes | Use weighted voting or SMOTE |
| High dimensionality | Dimensionality reduction (PCA) |
| Slow predictions | Use KD-tree, Ball tree, or LSH |

## 12. References

- [Wikipedia: K-Nearest Neighbors](https://en.wikipedia.org/wiki/K-nearest_neighbors_algorithm)
- Cover, T. and Hart, P. "Nearest neighbor pattern classification" (1967)
- Hastie, Tibshirani, Friedman. "Elements of Statistical Learning"
