# Random Forest

## Overview
- **Category**: Ensemble Learning / Classification & Regression
- **Complexity**: Training: O(n×m×d×log n) | Prediction: O(T×log n)
- **Type**: Bagging ensemble of decision trees
- **Source Files**: [machine_learning/random_forest.py](../../../machine_learning/random_forest.py)

## 1. Mathematical Foundation

### 1.1 Ensemble Learning

Random Forest builds multiple decision trees and aggregates their predictions:

$$
\hat{y} = \frac{1}{T}\sum_{t=1}^{T}h_t(\mathbf{x})
$$

where $T$ = number of trees, $h_t$ = individual tree prediction.

### 1.2 Bagging (Bootstrap Aggregating)

For each tree $t$:
1. Sample $n$ instances with replacement (bootstrap)
2. Train tree on bootstrap sample

**Out-of-Bag (OOB) samples**: ~36.8% of data not in bootstrap (for validation)

$$
P(\text{not selected}) = \left(1 - \frac{1}{n}\right)^n \approx \frac{1}{e} \approx 0.368
$$

### 1.3 Random Feature Selection

At each split, consider only $m$ random features:

**Classification**: $m = \sqrt{d}$

**Regression**: $m = d/3$

### 1.4 Classification: Majority Voting

$$
\hat{y} = \text{mode}\{h_1(\mathbf{x}), h_2(\mathbf{x}), ..., h_T(\mathbf{x})\}
$$

**Probability estimates**:
$$
P(y=k|\mathbf{x}) = \frac{1}{T}\sum_{t=1}^{T}\mathbb{1}[h_t(\mathbf{x}) = k]
$$

### 1.5 Regression: Averaging

$$
\hat{y} = \frac{1}{T}\sum_{t=1}^{T}h_t(\mathbf{x})
$$

### 1.6 Variance Reduction

For correlated trees with correlation $\rho$:

$$
\text{Var}(\bar{h}) = \rho\sigma^2 + \frac{1-\rho}{T}\sigma^2
$$

Random feature selection reduces $\rho$, reducing variance.

### 1.7 Feature Importance

**Mean Decrease in Impurity (MDI)**:
$$
\text{Importance}(f) = \frac{1}{T}\sum_{t=1}^{T}\sum_{s \in S_t(f)} \Delta I(s)
$$

where $S_t(f)$ = splits using feature $f$ in tree $t$.

**Permutation Importance**:
$$
\text{Importance}(f) = \text{Score} - \text{Score}_{\text{permuted}(f)}
$$

## 2. Algorithm Components

### 2.1 Decision Tree Splitting

**Gini impurity**:
$$
G(S) = 1 - \sum_{k=1}^{K}p_k^2
$$

**Information gain** (entropy):
$$
IG(S, f) = H(S) - \sum_{v}\frac{|S_v|}{|S|}H(S_v)
$$

### 2.2 Stopping Criteria

- Maximum depth reached
- Minimum samples per leaf
- Minimum impurity decrease
- All samples same class

## 3. Pseudocode

```
ALGORITHM RandomForest(X, y, n_trees, max_features, max_depth)
    INPUT: Data X, labels y, number of trees, max features, max depth
    OUTPUT: Ensemble of decision trees
    
    forest ← []
    
    for t ← 1 to n_trees do
        // Bootstrap sample
        X_boot, y_boot ← BootstrapSample(X, y)
        
        // Train decision tree with random feature selection
        tree ← BuildTree(X_boot, y_boot, max_features, max_depth)
        
        forest.append(tree)
    
    return forest

ALGORITHM BuildTree(X, y, max_features, max_depth, depth=0)
    INPUT: Data, labels, parameters, current depth
    OUTPUT: Decision tree node
    
    // Check stopping criteria
    if depth ≥ max_depth or |X| < min_samples or IsPure(y) then
        return LeafNode(MostCommon(y))
    
    // Random feature selection
    features ← RandomSample(all_features, max_features)
    
    // Find best split
    best_feature, best_threshold ← FindBestSplit(X, y, features)
    
    if no valid split found then
        return LeafNode(MostCommon(y))
    
    // Split data
    left_idx ← X[:, best_feature] ≤ best_threshold
    right_idx ← NOT left_idx
    
    // Recursively build subtrees
    left_child ← BuildTree(X[left_idx], y[left_idx], max_features, max_depth, depth+1)
    right_child ← BuildTree(X[right_idx], y[right_idx], max_features, max_depth, depth+1)
    
    return SplitNode(best_feature, best_threshold, left_child, right_child)

ALGORITHM Predict(forest, x)
    INPUT: Ensemble of trees, query point
    OUTPUT: Predicted class
    
    votes ← []
    
    for tree in forest do
        vote ← TreePredict(tree, x)
        votes.append(vote)
    
    return MajorityVote(votes)

ALGORITHM ComputeFeatureImportance(forest)
    INPUT: Trained random forest
    OUTPUT: Feature importance scores
    
    importances ← zeros(n_features)
    
    for tree in forest do
        for node in tree do
            if node is SplitNode then
                feature ← node.feature
                importance_gain ← node.n_samples × (node.impurity - 
                    node.left.n_samples/node.n_samples × node.left.impurity -
                    node.right.n_samples/node.n_samples × node.right.impurity)
                importances[feature] += importance_gain
    
    // Normalize
    importances ← importances / sum(importances)
    
    return importances
```

## 4. Step-by-Step Example

### Example: Classification with 3 Trees

**Data** (4 samples):
| ID | X₁ | X₂ | Label |
|----|-----|-----|-------|
| 1 | 1 | 2 | A |
| 2 | 2 | 3 | A |
| 3 | 5 | 4 | B |
| 4 | 6 | 5 | B |

**Tree 1** (bootstrap: {1,1,3,4}):
- Uses X₁ for split
- Split at X₁ = 3: Left→A, Right→B

**Tree 2** (bootstrap: {2,2,3,4}):
- Uses X₂ for split
- Split at X₂ = 3.5: Left→A, Right→B

**Tree 3** (bootstrap: {1,2,4,4}):
- Uses X₁ for split
- Split at X₁ = 4: Left→A, Right→B

**Prediction for new point (3, 4)**:
- Tree 1: X₁=3 ≤ 3 → A
- Tree 2: X₂=4 > 3.5 → B
- Tree 3: X₁=3 < 4 → A

**Final**: Majority vote = A (2 vs 1)

## 5. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Build single tree | O(n×d×log n) | O(n) |
| Build forest | O(T×n×m×log n) | O(T×n) |
| Single prediction | O(T×log n) | O(1) |
| Batch prediction | O(N×T×log n) | O(N) |
| Feature importance | O(T×nodes) | O(d) |

Where T = trees, n = samples, d = features, m = max_features.

## 6. Visual Representation

### 6.1 Random Forest Structure

```
                    Random Forest
                         │
        ┌────────────────┼────────────────┐
        │                │                │
    ┌───▼───┐        ┌───▼───┐        ┌───▼───┐
    │ Tree₁ │        │ Tree₂ │        │ Tree₃ │
    │       │        │       │        │       │
    │  X₁<3 │        │  X₂<4 │        │  X₃<2 │
    │  / \  │        │  / \  │        │  / \  │
    │ A   B │        │ A   B │        │ B   A │
    └───────┘        └───────┘        └───────┘
        │                │                │
        ▼                ▼                ▼
       [A]              [B]              [A]
        │                │                │
        └────────────────┼────────────────┘
                         ▼
                   Majority Vote
                        [A]
```

### 6.2 Bootstrap Sampling

```
Original:  [1, 2, 3, 4, 5, 6, 7, 8]

Bootstrap 1: [2, 2, 4, 5, 5, 7, 7, 8]  ← With replacement
Bootstrap 2: [1, 1, 3, 3, 4, 6, 8, 8]
Bootstrap 3: [1, 2, 3, 4, 5, 5, 6, 7]

OOB (not selected):
Bootstrap 1: {1, 3, 6}
Bootstrap 2: {2, 5, 7}
Bootstrap 3: {8}
```

### 6.3 Random Feature Selection

```
All features: [X₁, X₂, X₃, X₄, X₅, X₆]

Tree 1, Split 1: Consider [X₂, X₄] → Best: X₂
Tree 1, Split 2: Consider [X₁, X₅] → Best: X₅
Tree 2, Split 1: Consider [X₃, X₆] → Best: X₃
...
```

## 7. Implementation

```python
from typing import List, Tuple, Optional, Dict, Any
import random
import math
from collections import Counter


class TreeNode:
    """Decision tree node."""
    
    def __init__(self):
        self.feature: Optional[int] = None
        self.threshold: Optional[float] = None
        self.left: Optional['TreeNode'] = None
        self.right: Optional['TreeNode'] = None
        self.value: Optional[any] = None  # For leaf nodes
        self.n_samples: int = 0
        self.impurity: float = 0.0


class DecisionTree:
    """
    Decision tree for Random Forest.
    """
    
    def __init__(
        self,
        max_depth: int = 10,
        min_samples_split: int = 2,
        min_samples_leaf: int = 1,
        max_features: Optional[int] = None,
        criterion: str = 'gini',
        random_state: Optional[int] = None
    ):
        self.max_depth = max_depth
        self.min_samples_split = min_samples_split
        self.min_samples_leaf = min_samples_leaf
        self.max_features = max_features
        self.criterion = criterion
        
        if random_state is not None:
            random.seed(random_state)
        
        self.root: Optional[TreeNode] = None
        self.n_features: int = 0
        self.classes: List[any] = []
    
    def _gini(self, y: List) -> float:
        """Compute Gini impurity."""
        if len(y) == 0:
            return 0.0
        
        counts = Counter(y)
        n = len(y)
        return 1 - sum((c / n) ** 2 for c in counts.values())
    
    def _entropy(self, y: List) -> float:
        """Compute entropy."""
        if len(y) == 0:
            return 0.0
        
        counts = Counter(y)
        n = len(y)
        entropy = 0.0
        
        for c in counts.values():
            p = c / n
            if p > 0:
                entropy -= p * math.log2(p)
        
        return entropy
    
    def _impurity(self, y: List) -> float:
        """Compute impurity based on criterion."""
        if self.criterion == 'gini':
            return self._gini(y)
        else:
            return self._entropy(y)
    
    def _best_split(
        self,
        X: List[List[float]],
        y: List,
        features: List[int]
    ) -> Tuple[Optional[int], Optional[float], float]:
        """Find best split among given features."""
        best_gain = -float('inf')
        best_feature = None
        best_threshold = None
        
        n = len(y)
        parent_impurity = self._impurity(y)
        
        for feature in features:
            # Get unique values for threshold candidates
            values = sorted(set(X[i][feature] for i in range(n)))
            
            for i in range(len(values) - 1):
                threshold = (values[i] + values[i + 1]) / 2
                
                # Split data
                left_idx = [j for j in range(n) if X[j][feature] <= threshold]
                right_idx = [j for j in range(n) if X[j][feature] > threshold]
                
                if len(left_idx) < self.min_samples_leaf or len(right_idx) < self.min_samples_leaf:
                    continue
                
                # Compute information gain
                y_left = [y[j] for j in left_idx]
                y_right = [y[j] for j in right_idx]
                
                left_impurity = self._impurity(y_left)
                right_impurity = self._impurity(y_right)
                
                gain = parent_impurity - (
                    len(left_idx) / n * left_impurity +
                    len(right_idx) / n * right_impurity
                )
                
                if gain > best_gain:
                    best_gain = gain
                    best_feature = feature
                    best_threshold = threshold
        
        return best_feature, best_threshold, best_gain
    
    def _build_tree(
        self,
        X: List[List[float]],
        y: List,
        depth: int
    ) -> TreeNode:
        """Recursively build decision tree."""
        node = TreeNode()
        node.n_samples = len(y)
        node.impurity = self._impurity(y)
        
        # Check stopping criteria
        if (depth >= self.max_depth or
            len(y) < self.min_samples_split or
            len(set(y)) == 1):
            node.value = Counter(y).most_common(1)[0][0]
            return node
        
        # Random feature selection
        if self.max_features is None:
            n_features = self.n_features
        else:
            n_features = min(self.max_features, self.n_features)
        
        features = random.sample(range(self.n_features), n_features)
        
        # Find best split
        best_feature, best_threshold, best_gain = self._best_split(X, y, features)
        
        if best_feature is None or best_gain <= 0:
            node.value = Counter(y).most_common(1)[0][0]
            return node
        
        node.feature = best_feature
        node.threshold = best_threshold
        
        # Split data
        left_idx = [i for i in range(len(y)) if X[i][best_feature] <= best_threshold]
        right_idx = [i for i in range(len(y)) if X[i][best_feature] > best_threshold]
        
        X_left = [X[i] for i in left_idx]
        y_left = [y[i] for i in left_idx]
        X_right = [X[i] for i in right_idx]
        y_right = [y[i] for i in right_idx]
        
        # Recursively build subtrees
        node.left = self._build_tree(X_left, y_left, depth + 1)
        node.right = self._build_tree(X_right, y_right, depth + 1)
        
        return node
    
    def fit(
        self,
        X: List[List[float]],
        y: List
    ) -> 'DecisionTree':
        """Train decision tree."""
        self.n_features = len(X[0])
        self.classes = list(set(y))
        self.root = self._build_tree(X, y, 0)
        return self
    
    def _predict_single(self, x: List[float], node: TreeNode) -> any:
        """Predict single sample."""
        if node.value is not None:
            return node.value
        
        if x[node.feature] <= node.threshold:
            return self._predict_single(x, node.left)
        else:
            return self._predict_single(x, node.right)
    
    def predict(self, X: List[List[float]]) -> List:
        """Predict class labels."""
        return [self._predict_single(x, self.root) for x in X]


class RandomForestClassifier:
    """
    Random Forest classifier.
    
    Ensemble of decision trees with bootstrap sampling and random feature selection.
    """
    
    def __init__(
        self,
        n_estimators: int = 100,
        max_depth: int = 10,
        min_samples_split: int = 2,
        min_samples_leaf: int = 1,
        max_features: str = 'sqrt',
        criterion: str = 'gini',
        bootstrap: bool = True,
        oob_score: bool = False,
        n_jobs: int = 1,
        random_state: Optional[int] = None
    ):
        """
        Initialize Random Forest.
        
        Args:
            n_estimators: Number of trees
            max_depth: Maximum tree depth
            min_samples_split: Minimum samples to split
            min_samples_leaf: Minimum samples in leaf
            max_features: 'sqrt', 'log2', int, or float
            criterion: 'gini' or 'entropy'
            bootstrap: Use bootstrap sampling
            oob_score: Compute out-of-bag score
            n_jobs: Parallel jobs (not implemented)
            random_state: Random seed
        """
        self.n_estimators = n_estimators
        self.max_depth = max_depth
        self.min_samples_split = min_samples_split
        self.min_samples_leaf = min_samples_leaf
        self.max_features = max_features
        self.criterion = criterion
        self.bootstrap = bootstrap
        self.oob_score = oob_score
        self.n_jobs = n_jobs
        self.random_state = random_state
        
        self.trees: List[DecisionTree] = []
        self.oob_indices: List[List[int]] = []
        self.oob_score_: float = 0.0
        self.classes_: List = []
        self.feature_importances_: List[float] = []
    
    def _get_max_features(self, n_features: int) -> int:
        """Determine number of features to consider."""
        if isinstance(self.max_features, int):
            return min(self.max_features, n_features)
        elif isinstance(self.max_features, float):
            return int(self.max_features * n_features)
        elif self.max_features == 'sqrt':
            return int(math.sqrt(n_features))
        elif self.max_features == 'log2':
            return int(math.log2(n_features))
        else:
            return n_features
    
    def _bootstrap_sample(
        self,
        X: List[List[float]],
        y: List
    ) -> Tuple[List[List[float]], List, List[int]]:
        """Create bootstrap sample and track OOB indices."""
        n = len(X)
        indices = [random.randint(0, n - 1) for _ in range(n)]
        
        X_boot = [X[i] for i in indices]
        y_boot = [y[i] for i in indices]
        
        # OOB indices
        in_bag = set(indices)
        oob = [i for i in range(n) if i not in in_bag]
        
        return X_boot, y_boot, oob
    
    def fit(
        self,
        X: List[List[float]],
        y: List
    ) -> 'RandomForestClassifier':
        """
        Train random forest.
        
        >>> rf = RandomForestClassifier(n_estimators=10, max_depth=5, random_state=42)
        >>> X = [[1, 2], [2, 3], [3, 4], [4, 5], [5, 6], [6, 7]]
        >>> y = [0, 0, 0, 1, 1, 1]
        >>> rf.fit(X, y) is not None
        True
        """
        if self.random_state is not None:
            random.seed(self.random_state)
        
        n_features = len(X[0])
        max_features = self._get_max_features(n_features)
        
        self.classes_ = list(sorted(set(y)))
        self.trees = []
        self.oob_indices = []
        
        for _ in range(self.n_estimators):
            if self.bootstrap:
                X_boot, y_boot, oob = self._bootstrap_sample(X, y)
            else:
                X_boot, y_boot, oob = X, y, []
            
            tree = DecisionTree(
                max_depth=self.max_depth,
                min_samples_split=self.min_samples_split,
                min_samples_leaf=self.min_samples_leaf,
                max_features=max_features,
                criterion=self.criterion
            )
            tree.fit(X_boot, y_boot)
            
            self.trees.append(tree)
            self.oob_indices.append(oob)
        
        # Compute OOB score
        if self.oob_score and self.bootstrap:
            self._compute_oob_score(X, y)
        
        # Compute feature importances
        self._compute_feature_importances(n_features)
        
        return self
    
    def _compute_oob_score(self, X: List[List[float]], y: List):
        """Compute out-of-bag score."""
        n = len(X)
        oob_predictions = [[] for _ in range(n)]
        
        for tree_idx, tree in enumerate(self.trees):
            for i in self.oob_indices[tree_idx]:
                pred = tree.predict([X[i]])[0]
                oob_predictions[i].append(pred)
        
        # Compute accuracy on OOB samples
        correct = 0
        total = 0
        
        for i in range(n):
            if oob_predictions[i]:
                pred = Counter(oob_predictions[i]).most_common(1)[0][0]
                if pred == y[i]:
                    correct += 1
                total += 1
        
        self.oob_score_ = correct / total if total > 0 else 0.0
    
    def _compute_feature_importances(self, n_features: int):
        """Compute feature importances."""
        importances = [0.0] * n_features
        
        for tree in self.trees:
            self._traverse_tree(tree.root, importances)
        
        # Normalize
        total = sum(importances)
        if total > 0:
            self.feature_importances_ = [imp / total for imp in importances]
        else:
            self.feature_importances_ = [1 / n_features] * n_features
    
    def _traverse_tree(self, node: TreeNode, importances: List[float]):
        """Traverse tree to accumulate feature importances."""
        if node is None or node.value is not None:
            return
        
        if node.feature is not None:
            # Weighted impurity decrease
            left_samples = node.left.n_samples if node.left else 0
            right_samples = node.right.n_samples if node.right else 0
            
            left_impurity = node.left.impurity if node.left else 0
            right_impurity = node.right.impurity if node.right else 0
            
            importance = node.n_samples * (
                node.impurity -
                left_samples / max(node.n_samples, 1) * left_impurity -
                right_samples / max(node.n_samples, 1) * right_impurity
            )
            importances[node.feature] += importance
        
        self._traverse_tree(node.left, importances)
        self._traverse_tree(node.right, importances)
    
    def predict(self, X: List[List[float]]) -> List:
        """
        Predict class labels.
        
        >>> rf = RandomForestClassifier(n_estimators=10, random_state=42)
        >>> rf.fit([[1, 2], [2, 3], [5, 6], [6, 7]], [0, 0, 1, 1])
        <...>
        >>> preds = rf.predict([[1.5, 2.5], [5.5, 6.5]])
        >>> preds[0] == 0 and preds[1] == 1
        True
        """
        predictions = []
        
        for x in X:
            votes = [tree.predict([x])[0] for tree in self.trees]
            pred = Counter(votes).most_common(1)[0][0]
            predictions.append(pred)
        
        return predictions
    
    def predict_proba(self, X: List[List[float]]) -> List[List[float]]:
        """
        Predict class probabilities.
        """
        probas = []
        
        for x in X:
            votes = [tree.predict([x])[0] for tree in self.trees]
            counts = Counter(votes)
            
            proba = [
                counts.get(cls, 0) / self.n_estimators
                for cls in self.classes_
            ]
            probas.append(proba)
        
        return probas
    
    def score(
        self,
        X: List[List[float]],
        y: List
    ) -> float:
        """Compute accuracy."""
        predictions = self.predict(X)
        correct = sum(1 for p, yi in zip(predictions, y) if p == yi)
        return correct / len(y)


class RandomForestRegressor:
    """
    Random Forest regressor.
    """
    
    def __init__(
        self,
        n_estimators: int = 100,
        max_depth: int = 10,
        min_samples_split: int = 2,
        max_features: str = 'sqrt',
        bootstrap: bool = True,
        random_state: Optional[int] = None
    ):
        self.n_estimators = n_estimators
        self.max_depth = max_depth
        self.min_samples_split = min_samples_split
        self.max_features = max_features
        self.bootstrap = bootstrap
        self.random_state = random_state
        
        self.trees: List = []
    
    def fit(
        self,
        X: List[List[float]],
        y: List[float]
    ) -> 'RandomForestRegressor':
        """Train random forest regressor."""
        if self.random_state is not None:
            random.seed(self.random_state)
        
        n_features = len(X[0])
        if self.max_features == 'sqrt':
            max_features = int(math.sqrt(n_features))
        elif self.max_features == 'log2':
            max_features = int(math.log2(n_features))
        else:
            max_features = n_features // 3
        
        self.trees = []
        
        for _ in range(self.n_estimators):
            if self.bootstrap:
                n = len(X)
                indices = [random.randint(0, n - 1) for _ in range(n)]
                X_boot = [X[i] for i in indices]
                y_boot = [y[i] for i in indices]
            else:
                X_boot, y_boot = X, y
            
            tree = RegressionTree(
                max_depth=self.max_depth,
                min_samples_split=self.min_samples_split,
                max_features=max_features
            )
            tree.fit(X_boot, y_boot)
            self.trees.append(tree)
        
        return self
    
    def predict(self, X: List[List[float]]) -> List[float]:
        """Predict values by averaging tree predictions."""
        predictions = []
        
        for x in X:
            tree_preds = [tree.predict([x])[0] for tree in self.trees]
            predictions.append(sum(tree_preds) / len(tree_preds))
        
        return predictions


class RegressionTree:
    """Simple regression tree for Random Forest."""
    
    def __init__(
        self,
        max_depth: int = 10,
        min_samples_split: int = 2,
        max_features: Optional[int] = None
    ):
        self.max_depth = max_depth
        self.min_samples_split = min_samples_split
        self.max_features = max_features
        self.root = None
    
    def _mse(self, y: List[float]) -> float:
        """Mean squared error."""
        if len(y) == 0:
            return 0.0
        mean = sum(y) / len(y)
        return sum((yi - mean) ** 2 for yi in y) / len(y)
    
    def _build_tree(
        self,
        X: List[List[float]],
        y: List[float],
        depth: int
    ) -> TreeNode:
        node = TreeNode()
        node.n_samples = len(y)
        
        if depth >= self.max_depth or len(y) < self.min_samples_split:
            node.value = sum(y) / len(y) if y else 0
            return node
        
        n_features = len(X[0])
        if self.max_features:
            features = random.sample(range(n_features), min(self.max_features, n_features))
        else:
            features = list(range(n_features))
        
        best_feature, best_threshold, best_mse = None, None, float('inf')
        
        for feature in features:
            values = sorted(set(X[i][feature] for i in range(len(X))))
            
            for i in range(len(values) - 1):
                threshold = (values[i] + values[i + 1]) / 2
                
                left_idx = [j for j in range(len(y)) if X[j][feature] <= threshold]
                right_idx = [j for j in range(len(y)) if X[j][feature] > threshold]
                
                if not left_idx or not right_idx:
                    continue
                
                y_left = [y[j] for j in left_idx]
                y_right = [y[j] for j in right_idx]
                
                mse = (len(left_idx) * self._mse(y_left) + 
                       len(right_idx) * self._mse(y_right)) / len(y)
                
                if mse < best_mse:
                    best_mse = mse
                    best_feature = feature
                    best_threshold = threshold
        
        if best_feature is None:
            node.value = sum(y) / len(y) if y else 0
            return node
        
        node.feature = best_feature
        node.threshold = best_threshold
        
        left_idx = [i for i in range(len(y)) if X[i][best_feature] <= best_threshold]
        right_idx = [i for i in range(len(y)) if X[i][best_feature] > best_threshold]
        
        node.left = self._build_tree(
            [X[i] for i in left_idx],
            [y[i] for i in left_idx],
            depth + 1
        )
        node.right = self._build_tree(
            [X[i] for i in right_idx],
            [y[i] for i in right_idx],
            depth + 1
        )
        
        return node
    
    def fit(self, X: List[List[float]], y: List[float]) -> 'RegressionTree':
        self.root = self._build_tree(X, y, 0)
        return self
    
    def predict(self, X: List[List[float]]) -> List[float]:
        def predict_single(x, node):
            if node.value is not None:
                return node.value
            if x[node.feature] <= node.threshold:
                return predict_single(x, node.left)
            return predict_single(x, node.right)
        
        return [predict_single(x, self.root) for x in X]
```

## 8. Applications

### 8.1 Common Use Cases

- Credit scoring
- Medical diagnosis
- Customer churn prediction
- Fraud detection
- Species classification
- Stock price prediction
- Feature selection

### 8.2 Advantages

- No feature scaling needed
- Handles non-linear relationships
- Built-in feature importance
- Resistant to overfitting
- Handles missing values (with modifications)
- Works with both numerical and categorical data

## 9. Real-World Software Engineering Applications

### 9.1 Production Example: Customer Churn Prediction

```python
from typing import List, Dict, Tuple, Optional
import random
import math
from datetime import datetime, timedelta
from collections import Counter


class ChurnPredictionForest:
    """
    Production Random Forest for customer churn prediction.
    """
    
    def __init__(
        self,
        n_estimators: int = 100,
        max_depth: int = 15,
        min_samples_leaf: int = 10,
        class_weight: Optional[Dict] = None
    ):
        """
        Initialize churn predictor.
        
        Args:
            n_estimators: Number of trees
            max_depth: Maximum tree depth
            min_samples_leaf: Minimum samples in leaf
            class_weight: Handle imbalanced classes
        """
        self.n_estimators = n_estimators
        self.max_depth = max_depth
        self.min_samples_leaf = min_samples_leaf
        self.class_weight = class_weight or {0: 1.0, 1: 1.0}
        
        self.rf: Optional[RandomForestClassifier] = None
        
        # Feature metadata
        self.feature_names: List[str] = []
        self.feature_means: Dict[str, float] = {}
        self.feature_stds: Dict[str, float] = {}
        
        # Model info
        self.training_date: Optional[datetime] = None
        self.n_samples: int = 0
        self.churn_rate: float = 0.0
    
    def _extract_features(
        self,
        customer: dict
    ) -> Tuple[List[float], List[str]]:
        """
        Extract features from customer data.
        """
        features = []
        names = []
        
        # Tenure features
        tenure = customer.get('tenure_months', 0)
        features.extend([tenure, tenure ** 2])
        names.extend(['tenure_months', 'tenure_squared'])
        
        # Financial features
        monthly_charges = customer.get('monthly_charges', 0)
        total_charges = customer.get('total_charges', 0)
        features.extend([
            monthly_charges,
            total_charges,
            total_charges / max(tenure, 1)  # Avg monthly
        ])
        names.extend(['monthly_charges', 'total_charges', 'avg_monthly'])
        
        # Contract features
        contract = customer.get('contract_type', 'month')
        features.extend([
            1 if contract == 'month' else 0,
            1 if contract == 'one_year' else 0,
            1 if contract == 'two_year' else 0
        ])
        names.extend(['contract_month', 'contract_1yr', 'contract_2yr'])
        
        # Usage features
        features.extend([
            customer.get('support_tickets', 0),
            customer.get('support_tickets', 0) / max(tenure, 1),
            customer.get('logins_last_month', 0),
            customer.get('feature_usage_score', 0.5)
        ])
        names.extend(['support_tickets', 'tickets_per_month', 
                     'logins_last_month', 'feature_usage'])
        
        # Engagement features
        features.extend([
            customer.get('days_since_last_login', 0),
            1 if customer.get('days_since_last_login', 0) > 30 else 0,
            customer.get('email_opt_in', 0),
            customer.get('nps_score', 5) / 10
        ])
        names.extend(['days_since_login', 'inactive_30d',
                     'email_opt_in', 'nps_normalized'])
        
        # Payment features
        features.extend([
            customer.get('late_payments', 0),
            1 if customer.get('payment_method') == 'auto' else 0,
            customer.get('payment_failures', 0)
        ])
        names.extend(['late_payments', 'auto_pay', 'payment_failures'])
        
        return features, names
    
    def _handle_imbalance(
        self,
        X: List[List[float]],
        y: List[int]
    ) -> Tuple[List[List[float]], List[int]]:
        """Handle class imbalance with SMOTE-like oversampling."""
        churned = [(X[i], y[i]) for i in range(len(y)) if y[i] == 1]
        not_churned = [(X[i], y[i]) for i in range(len(y)) if y[i] == 0]
        
        # Oversample minority class
        if len(churned) < len(not_churned):
            ratio = len(not_churned) // len(churned)
            
            new_samples = []
            for _ in range(min(ratio - 1, 5)):
                for x, _ in churned:
                    # Add noise
                    noisy = [xi + random.gauss(0, 0.01) for xi in x]
                    new_samples.append((noisy, 1))
            
            all_samples = not_churned + churned + new_samples
        else:
            all_samples = not_churned + churned
        
        random.shuffle(all_samples)
        X_balanced = [s[0] for s in all_samples]
        y_balanced = [s[1] for s in all_samples]
        
        return X_balanced, y_balanced
    
    def fit(
        self,
        customers: List[dict],
        churned: List[int]
    ) -> 'ChurnPredictionForest':
        """
        Train churn prediction model.
        
        Args:
            customers: Customer data dictionaries
            churned: 1 if churned, 0 otherwise
        """
        # Extract features
        X = []
        for customer in customers:
            features, names = self._extract_features(customer)
            X.append(features)
        
        self.feature_names = names
        
        # Handle imbalance
        X_balanced, y_balanced = self._handle_imbalance(X, churned)
        
        # Train random forest
        self.rf = RandomForestClassifier(
            n_estimators=self.n_estimators,
            max_depth=self.max_depth,
            min_samples_leaf=self.min_samples_leaf,
            max_features='sqrt',
            oob_score=True,
            random_state=42
        )
        self.rf.fit(X_balanced, y_balanced)
        
        # Store metadata
        self.training_date = datetime.now()
        self.n_samples = len(customers)
        self.churn_rate = sum(churned) / len(churned)
        
        return self
    
    def predict_proba(
        self,
        customers: List[dict]
    ) -> List[float]:
        """
        Predict churn probability.
        
        Returns:
            Probability of churning for each customer.
        """
        X = [self._extract_features(c)[0] for c in customers]
        probas = self.rf.predict_proba(X)
        return [p[1] for p in probas]  # Probability of class 1 (churn)
    
    def predict(
        self,
        customers: List[dict],
        threshold: float = 0.5
    ) -> List[int]:
        """Predict churn (0 or 1)."""
        probas = self.predict_proba(customers)
        return [1 if p >= threshold else 0 for p in probas]
    
    def identify_at_risk(
        self,
        customers: List[dict],
        risk_threshold: float = 0.6
    ) -> List[Tuple[int, float, List[str]]]:
        """
        Identify at-risk customers with reasons.
        
        Returns:
            List of (customer_idx, churn_prob, risk_factors)
        """
        X = [self._extract_features(c)[0] for c in customers]
        probas = self.rf.predict_proba(X)
        
        at_risk = []
        
        for i, (customer, proba) in enumerate(zip(customers, probas)):
            churn_prob = proba[1]
            
            if churn_prob >= risk_threshold:
                # Identify risk factors
                risk_factors = self._identify_risk_factors(customer)
                at_risk.append((i, churn_prob, risk_factors))
        
        # Sort by churn probability
        at_risk.sort(key=lambda x: x[1], reverse=True)
        
        return at_risk
    
    def _identify_risk_factors(
        self,
        customer: dict
    ) -> List[str]:
        """Identify risk factors for a customer."""
        factors = []
        
        if customer.get('tenure_months', 0) < 6:
            factors.append('New customer (< 6 months)')
        
        if customer.get('contract_type') == 'month':
            factors.append('Month-to-month contract')
        
        if customer.get('support_tickets', 0) > 3:
            factors.append('High support tickets')
        
        if customer.get('days_since_last_login', 0) > 30:
            factors.append('Inactive for 30+ days')
        
        if customer.get('nps_score', 5) < 5:
            factors.append('Low NPS score')
        
        if customer.get('late_payments', 0) > 0:
            factors.append('History of late payments')
        
        if customer.get('feature_usage_score', 0.5) < 0.3:
            factors.append('Low feature adoption')
        
        return factors
    
    def get_feature_importance(self) -> List[Tuple[str, float]]:
        """
        Get feature importance scores.
        
        Returns:
            List of (feature_name, importance) sorted by importance.
        """
        importances = list(zip(
            self.feature_names,
            self.rf.feature_importances_
        ))
        return sorted(importances, key=lambda x: x[1], reverse=True)
    
    def get_retention_recommendations(
        self,
        customer: dict
    ) -> Dict[str, any]:
        """
        Generate retention recommendations.
        """
        churn_prob = self.predict_proba([customer])[0]
        risk_factors = self._identify_risk_factors(customer)
        
        recommendations = []
        priority = 'low'
        
        if churn_prob >= 0.8:
            priority = 'critical'
            recommendations.extend([
                'Immediate personal outreach by account manager',
                'Offer significant discount or free month',
                'Executive escalation for enterprise accounts'
            ])
        elif churn_prob >= 0.6:
            priority = 'high'
            recommendations.extend([
                'Schedule check-in call',
                'Send personalized retention offer',
                'Invite to product training session'
            ])
        elif churn_prob >= 0.4:
            priority = 'medium'
            recommendations.extend([
                'Include in re-engagement email campaign',
                'Highlight unused features',
                'Offer upgrade incentive'
            ])
        
        # Specific recommendations based on risk factors
        if 'Low feature adoption' in risk_factors:
            recommendations.append('Provide personalized onboarding')
        if 'Inactive for 30+ days' in risk_factors:
            recommendations.append('Send "We miss you" campaign')
        if 'Month-to-month contract' in risk_factors:
            recommendations.append('Offer annual contract discount')
        
        return {
            'customer_id': customer.get('customer_id'),
            'churn_probability': churn_prob,
            'risk_level': priority,
            'risk_factors': risk_factors,
            'recommendations': recommendations
        }
    
    def evaluate(
        self,
        customers: List[dict],
        actual_churn: List[int]
    ) -> Dict[str, float]:
        """
        Evaluate model performance.
        """
        predictions = self.predict(customers)
        probas = self.predict_proba(customers)
        
        tp = sum(1 for p, a in zip(predictions, actual_churn) if p == 1 and a == 1)
        tn = sum(1 for p, a in zip(predictions, actual_churn) if p == 0 and a == 0)
        fp = sum(1 for p, a in zip(predictions, actual_churn) if p == 1 and a == 0)
        fn = sum(1 for p, a in zip(predictions, actual_churn) if p == 0 and a == 1)
        
        accuracy = (tp + tn) / len(actual_churn)
        precision = tp / (tp + fp) if tp + fp > 0 else 0
        recall = tp / (tp + fn) if tp + fn > 0 else 0
        f1 = 2 * precision * recall / (precision + recall) if precision + recall > 0 else 0
        
        # AUC
        auc = self._compute_auc(actual_churn, probas)
        
        return {
            'accuracy': accuracy,
            'precision': precision,
            'recall': recall,
            'f1_score': f1,
            'auc_roc': auc,
            'oob_score': self.rf.oob_score_,
            'true_positives': tp,
            'false_positives': fp,
            'true_negatives': tn,
            'false_negatives': fn
        }
    
    def _compute_auc(
        self,
        y_true: List[int],
        y_score: List[float]
    ) -> float:
        """Compute AUC-ROC."""
        pairs = sorted(zip(y_score, y_true), reverse=True)
        
        n_pos = sum(y_true)
        n_neg = len(y_true) - n_pos
        
        if n_pos == 0 or n_neg == 0:
            return 0.5
        
        auc = 0.0
        tp = 0
        
        for score, label in pairs:
            if label == 1:
                tp += 1
            else:
                auc += tp
        
        return auc / (n_pos * n_neg)


# Demo
if __name__ == "__main__":
    print("Random Forest Demo")
    print("=" * 50)
    
    # Simple classification
    print("\n1. Basic Classification:")
    random.seed(42)
    
    X_train = [[random.gauss(0, 1), random.gauss(0, 1)] for _ in range(100)]
    y_train = [1 if x[0] + x[1] > 0 else 0 for x in X_train]
    
    rf = RandomForestClassifier(
        n_estimators=10,
        max_depth=5,
        random_state=42
    )
    rf.fit(X_train, y_train)
    
    accuracy = rf.score(X_train, y_train)
    print(f"   Training accuracy: {accuracy:.2%}")
    print(f"   OOB score: {rf.oob_score_:.2%}")
    
    print("\n   Feature importances:")
    for i, imp in enumerate(rf.feature_importances_):
        print(f"   Feature {i}: {imp:.3f}")
    
    # Churn prediction example
    print("\n2. Churn Prediction:")
    
    customers = [
        {'tenure_months': 24, 'monthly_charges': 50, 'total_charges': 1200,
         'contract_type': 'one_year', 'support_tickets': 1, 
         'days_since_last_login': 5, 'nps_score': 8},
        {'tenure_months': 2, 'monthly_charges': 70, 'total_charges': 140,
         'contract_type': 'month', 'support_tickets': 5,
         'days_since_last_login': 45, 'nps_score': 3},
    ]
    
    churn_model = ChurnPredictionForest(n_estimators=10, max_depth=5)
    
    # Would need more data for meaningful training
    print("   Churn model initialized")
    print("   (Would need more data for training)")
```

## 10. Comparison

| Aspect | Random Forest | Single Tree | Gradient Boosting |
|--------|--------------|-------------|-------------------|
| Variance | Low | High | Low |
| Bias | Medium | Low | Very Low |
| Training time | Medium | Fast | Slow |
| Interpretability | Low | High | Low |
| Parallelizable | Yes | N/A | No |
| Feature importance | Yes | Yes | Yes |

## 11. Best Practices

1. **Start with 100+ trees** (more is usually better)
2. **Don't limit depth too much** (RF resists overfitting)
3. **Use sqrt(n_features)** for classification
4. **Enable OOB score** for free validation
5. **Check feature importances** for insights
6. **Parallelize training** for large datasets

## 12. References

- [Wikipedia: Random Forest](https://en.wikipedia.org/wiki/Random_forest)
- Breiman, L. "Random Forests" (2001)
- Hastie, T. et al. "The Elements of Statistical Learning" (2009)
