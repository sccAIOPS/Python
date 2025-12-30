# Decision Trees

## Overview
- **Category**: Supervised Learning - Classification & Regression
- **Complexity**: Time: O(n²d) training | O(d) prediction | Space: O(nodes)
- **Type**: Non-parametric, Tree-based
- **Source Files**: [machine_learning/decision_tree.py](../../../machine_learning/decision_tree.py)

## 1. Mathematical Foundation

### 1.1 Tree Structure

A decision tree recursively partitions feature space using axis-aligned splits:

```
Root Node
├── Split on feature j at threshold t
│   ├── Left subtree: x[j] ≤ t
│   └── Right subtree: x[j] > t
└── Continue until stopping criteria
```

### 1.2 Impurity Measures (Classification)

**Gini Impurity**:
$$
G(p) = \sum_{k=1}^{K} p_k(1 - p_k) = 1 - \sum_{k=1}^{K} p_k^2
$$

**Entropy** (Information Gain):
$$
H(p) = -\sum_{k=1}^{K} p_k \log_2(p_k)
$$

where $p_k$ is the proportion of class $k$ in the node.

**Comparison**:
- Gini: Maximum at $p = 0.5$ (binary), value 0.5
- Entropy: Maximum at $p = 0.5$ (binary), value 1.0
- Gini is faster to compute

### 1.3 Information Gain

For a split dividing node $S$ into subsets $S_L$ and $S_R$:

$$
IG = H(S) - \frac{|S_L|}{|S|} H(S_L) - \frac{|S_R|}{|S|} H(S_R)
$$

Choose split that maximizes information gain.

### 1.4 Variance Reduction (Regression)

For regression, use variance as impurity:

$$
\text{Var}(S) = \frac{1}{|S|} \sum_{i \in S} (y_i - \bar{y})^2
$$

Variance reduction:
$$
\Delta \text{Var} = \text{Var}(S) - \frac{|S_L|}{|S|} \text{Var}(S_L) - \frac{|S_R|}{|S|} \text{Var}(S_R)
$$

### 1.5 Splitting Algorithms

| Algorithm | Criterion | Features |
|-----------|-----------|----------|
| ID3 | Information Gain | Categorical |
| C4.5 | Gain Ratio | Categorical + Continuous |
| CART | Gini / Variance | Binary splits |

## 2. Pseudocode

```
ALGORITHM BuildTree(S, features, depth)
    INPUT: Dataset S, available features, current depth
    OUTPUT: Decision tree node
    
    // Base cases
    if all samples in S have same class then
        return Leaf(class)
    
    if features is empty OR depth ≥ max_depth OR |S| < min_samples then
        return Leaf(majority_class(S))
    
    // Find best split
    best_gain ← 0
    best_feature ← None
    best_threshold ← None
    
    for each feature j in features do
        for each threshold t in unique values of S[j] do
            S_L ← {x ∈ S : x[j] ≤ t}
            S_R ← {x ∈ S : x[j] > t}
            
            if |S_L| < min_samples_leaf OR |S_R| < min_samples_leaf then
                continue
            
            gain ← Impurity(S) - (|S_L|/|S|) × Impurity(S_L) 
                                - (|S_R|/|S|) × Impurity(S_R)
            
            if gain > best_gain then
                best_gain ← gain
                best_feature ← j
                best_threshold ← t
    
    if best_gain = 0 then
        return Leaf(majority_class(S))
    
    // Create internal node
    node ← Node(feature=best_feature, threshold=best_threshold)
    
    S_L ← {x ∈ S : x[best_feature] ≤ best_threshold}
    S_R ← {x ∈ S : x[best_feature] > best_threshold}
    
    node.left ← BuildTree(S_L, features, depth + 1)
    node.right ← BuildTree(S_R, features, depth + 1)
    
    return node

ALGORITHM Predict(x, tree)
    if tree is Leaf then
        return tree.value
    
    if x[tree.feature] ≤ tree.threshold then
        return Predict(x, tree.left)
    else
        return Predict(x, tree.right)

ALGORITHM GiniImpurity(S)
    counts ← count occurrences of each class in S
    gini ← 1
    for each class c do
        p ← counts[c] / |S|
        gini ← gini - p²
    return gini
```

## 3. Step-by-Step Example

### Example: Classification Tree

**Data**:
| Outlook | Temp | Play? |
|---------|------|-------|
| Sunny | Hot | No |
| Sunny | Mild | No |
| Overcast | Hot | Yes |
| Rain | Mild | Yes |
| Rain | Cool | Yes |
| Overcast | Cool | Yes |

**Step 1**: Calculate root impurity
- Play=Yes: 4/6, Play=No: 2/6
- Gini = 1 - (4/6)² - (2/6)² = 0.444

**Step 2**: Evaluate splits

*Outlook split:*
- Sunny: No=2, Yes=0 → Gini = 0
- Overcast: No=0, Yes=2 → Gini = 0  
- Rain: No=0, Yes=2 → Gini = 0
- Weighted Gini = 0 (perfect split!)

**Step 3**: Build tree
```
        [Outlook]
       /    |    \
   Sunny  Over   Rain
     |     |       |
    No    Yes     Yes
```

## 4. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Training | O(n²d) or O(nd log n) | O(n + nodes) |
| Prediction | O(depth) ≈ O(log n) | O(1) |
| Memory | - | O(nodes × d) |

### 4.1 Training Breakdown

- For each node: O(n) samples
- For each feature: O(d)
- For each threshold: O(n) (or O(log n) with sorting)
- Total: O(n × d × n) = O(n²d)

With pre-sorting: O(nd log n)

## 5. Visual Representation

### 5.1 Decision Boundary

```
x₂ │
   │  ┌─────────┬───────────┐
   │  │  Class  │           │
   │  │    A    │  Class B  │
   │  ├─────────┤           │
   │  │         │───────────┤
   │  │ Class C │  Class A  │
   │  │         │           │
   └──┴─────────┴───────────┴──→ x₁
                ↑
           Split on x₁
```

### 5.2 Tree Structure

```
                    [x₁ ≤ 5?]
                   /         \
               Yes             No
              /                 \
       [x₂ ≤ 3?]              Class B
       /       \
    Yes         No
   /             \
Class A       Class C
```

## 6. Implementation

```python
from typing import List, Tuple, Optional, Dict, Any, Union
import math
from dataclasses import dataclass
from collections import Counter


@dataclass
class TreeNode:
    """Node in decision tree."""
    feature: Optional[int] = None       # Feature index for split
    threshold: Optional[float] = None   # Threshold for split
    value: Optional[Any] = None         # Prediction (leaf only)
    left: Optional['TreeNode'] = None
    right: Optional['TreeNode'] = None
    impurity: float = 0.0
    n_samples: int = 0


class DecisionTreeClassifier:
    """
    Decision Tree for classification.
    
    Uses Gini impurity or entropy as splitting criterion.
    """
    
    def __init__(
        self,
        criterion: str = 'gini',
        max_depth: Optional[int] = None,
        min_samples_split: int = 2,
        min_samples_leaf: int = 1,
        max_features: Optional[int] = None
    ):
        """
        Initialize decision tree classifier.
        
        Args:
            criterion: 'gini' or 'entropy'
            max_depth: Maximum tree depth
            min_samples_split: Minimum samples to split node
            min_samples_leaf: Minimum samples in leaf
            max_features: Features to consider per split
        """
        self.criterion = criterion
        self.max_depth = max_depth
        self.min_samples_split = min_samples_split
        self.min_samples_leaf = min_samples_leaf
        self.max_features = max_features
        
        self.tree: Optional[TreeNode] = None
        self.n_classes: int = 0
        self.n_features: int = 0
        self.classes: List[Any] = []
    
    def _gini(self, y: List[int]) -> float:
        """
        Compute Gini impurity.
        
        >>> dt = DecisionTreeClassifier()
        >>> dt._gini([0, 0, 0, 0])  # Pure
        0.0
        >>> abs(dt._gini([0, 0, 1, 1]) - 0.5) < 0.001  # Mixed
        True
        """
        if not y:
            return 0.0
        
        n = len(y)
        counts = Counter(y)
        
        gini = 1.0
        for count in counts.values():
            p = count / n
            gini -= p ** 2
        
        return gini
    
    def _entropy(self, y: List[int]) -> float:
        """
        Compute entropy.
        
        >>> dt = DecisionTreeClassifier()
        >>> dt._entropy([0, 0, 0, 0])  # Pure
        0.0
        >>> abs(dt._entropy([0, 0, 1, 1]) - 1.0) < 0.001  # Max entropy
        True
        """
        if not y:
            return 0.0
        
        n = len(y)
        counts = Counter(y)
        
        entropy = 0.0
        for count in counts.values():
            if count > 0:
                p = count / n
                entropy -= p * math.log2(p)
        
        return entropy
    
    def _impurity(self, y: List[int]) -> float:
        """Compute impurity using configured criterion."""
        if self.criterion == 'gini':
            return self._gini(y)
        return self._entropy(y)
    
    def _information_gain(
        self,
        y: List[int],
        y_left: List[int],
        y_right: List[int]
    ) -> float:
        """Compute information gain from split."""
        n = len(y)
        n_left = len(y_left)
        n_right = len(y_right)
        
        if n_left == 0 or n_right == 0:
            return 0.0
        
        parent_impurity = self._impurity(y)
        left_impurity = self._impurity(y_left)
        right_impurity = self._impurity(y_right)
        
        child_impurity = (n_left / n) * left_impurity + (n_right / n) * right_impurity
        
        return parent_impurity - child_impurity
    
    def _best_split(
        self,
        X: List[List[float]],
        y: List[int],
        feature_indices: List[int]
    ) -> Tuple[Optional[int], Optional[float], float]:
        """
        Find best split for node.
        
        Returns (best_feature, best_threshold, best_gain).
        """
        best_gain = 0.0
        best_feature = None
        best_threshold = None
        
        for feature in feature_indices:
            # Get unique thresholds
            values = sorted(set(X[i][feature] for i in range(len(X))))
            
            # Try midpoints between consecutive values
            thresholds = [
                (values[i] + values[i + 1]) / 2
                for i in range(len(values) - 1)
            ]
            
            for threshold in thresholds:
                # Split data
                left_indices = [
                    i for i in range(len(X))
                    if X[i][feature] <= threshold
                ]
                right_indices = [
                    i for i in range(len(X))
                    if X[i][feature] > threshold
                ]
                
                # Check minimum samples
                if (len(left_indices) < self.min_samples_leaf or
                    len(right_indices) < self.min_samples_leaf):
                    continue
                
                y_left = [y[i] for i in left_indices]
                y_right = [y[i] for i in right_indices]
                
                gain = self._information_gain(y, y_left, y_right)
                
                if gain > best_gain:
                    best_gain = gain
                    best_feature = feature
                    best_threshold = threshold
        
        return best_feature, best_threshold, best_gain
    
    def _build_tree(
        self,
        X: List[List[float]],
        y: List[int],
        depth: int
    ) -> TreeNode:
        """Recursively build decision tree."""
        n_samples = len(y)
        
        # Create node
        node = TreeNode(
            n_samples=n_samples,
            impurity=self._impurity(y)
        )
        
        # Stopping criteria
        unique_classes = set(y)
        
        if (len(unique_classes) == 1 or
            n_samples < self.min_samples_split or
            (self.max_depth is not None and depth >= self.max_depth)):
            # Leaf node
            node.value = Counter(y).most_common(1)[0][0]
            return node
        
        # Select features to consider
        if self.max_features is not None:
            import random
            feature_indices = random.sample(
                range(self.n_features),
                min(self.max_features, self.n_features)
            )
        else:
            feature_indices = list(range(self.n_features))
        
        # Find best split
        best_feature, best_threshold, best_gain = self._best_split(
            X, y, feature_indices
        )
        
        if best_gain == 0:
            # No good split found
            node.value = Counter(y).most_common(1)[0][0]
            return node
        
        # Split data
        left_indices = [
            i for i in range(len(X))
            if X[i][best_feature] <= best_threshold
        ]
        right_indices = [
            i for i in range(len(X))
            if X[i][best_feature] > best_threshold
        ]
        
        X_left = [X[i] for i in left_indices]
        y_left = [y[i] for i in left_indices]
        X_right = [X[i] for i in right_indices]
        y_right = [y[i] for i in right_indices]
        
        # Create internal node
        node.feature = best_feature
        node.threshold = best_threshold
        node.left = self._build_tree(X_left, y_left, depth + 1)
        node.right = self._build_tree(X_right, y_right, depth + 1)
        
        return node
    
    def fit(
        self,
        X: List[List[float]],
        y: List[int]
    ) -> 'DecisionTreeClassifier':
        """
        Build decision tree from training data.
        
        >>> dt = DecisionTreeClassifier(max_depth=2)
        >>> X = [[1, 2], [2, 1], [3, 4], [4, 3]]
        >>> y = [0, 0, 1, 1]
        >>> dt.fit(X, y)  # doctest: +ELLIPSIS
        <...DecisionTreeClassifier object at ...>
        >>> dt.predict([[1.5, 1.5], [3.5, 3.5]])
        [0, 1]
        """
        self.n_features = len(X[0])
        self.classes = sorted(set(y))
        self.n_classes = len(self.classes)
        
        # Map labels to indices
        label_map = {c: i for i, c in enumerate(self.classes)}
        y_mapped = [label_map[yi] for yi in y]
        
        self.tree = self._build_tree(X, y_mapped, depth=0)
        
        return self
    
    def _predict_single(self, x: List[float], node: TreeNode) -> int:
        """Predict class for single sample."""
        if node.value is not None:
            return node.value
        
        if x[node.feature] <= node.threshold:
            return self._predict_single(x, node.left)
        return self._predict_single(x, node.right)
    
    def predict(self, X: List[List[float]]) -> List[Any]:
        """
        Predict classes for samples.
        
        >>> dt = DecisionTreeClassifier(max_depth=3)
        >>> X = [[1], [2], [3], [4], [5], [6]]
        >>> y = ['a', 'a', 'a', 'b', 'b', 'b']
        >>> dt.fit(X, y)  # doctest: +ELLIPSIS
        <...>
        >>> dt.predict([[1.5], [5.5]])
        ['a', 'b']
        """
        predictions = []
        for x in X:
            idx = self._predict_single(x, self.tree)
            predictions.append(self.classes[idx])
        return predictions
    
    def predict_proba(
        self,
        X: List[List[float]]
    ) -> List[List[float]]:
        """Predict class probabilities."""
        probas = []
        for x in X:
            node = self.tree
            while node.value is None:
                if x[node.feature] <= node.threshold:
                    node = node.left
                else:
                    node = node.right
            
            # Get class distribution at leaf
            # (simplified - would need to track distribution)
            proba = [0.0] * self.n_classes
            proba[node.value] = 1.0
            probas.append(proba)
        
        return probas
    
    def get_depth(self) -> int:
        """Get actual tree depth."""
        def _depth(node: TreeNode) -> int:
            if node is None or node.value is not None:
                return 0
            return 1 + max(_depth(node.left), _depth(node.right))
        
        return _depth(self.tree)
    
    def get_n_leaves(self) -> int:
        """Count leaf nodes."""
        def _count_leaves(node: TreeNode) -> int:
            if node is None:
                return 0
            if node.value is not None:
                return 1
            return _count_leaves(node.left) + _count_leaves(node.right)
        
        return _count_leaves(self.tree)
    
    def feature_importances(self) -> List[float]:
        """
        Compute feature importances.
        
        Based on total impurity reduction.
        """
        importances = [0.0] * self.n_features
        total_samples = self.tree.n_samples
        
        def _compute(node: TreeNode):
            if node is None or node.value is not None:
                return
            
            # Impurity reduction from this split
            left_contrib = (node.left.n_samples / total_samples) * node.left.impurity
            right_contrib = (node.right.n_samples / total_samples) * node.right.impurity
            
            importance = (node.n_samples / total_samples) * node.impurity
            importance -= left_contrib + right_contrib
            
            importances[node.feature] += importance
            
            _compute(node.left)
            _compute(node.right)
        
        _compute(self.tree)
        
        # Normalize
        total = sum(importances)
        if total > 0:
            importances = [i / total for i in importances]
        
        return importances


class DecisionTreeRegressor:
    """
    Decision Tree for regression.
    
    Uses variance reduction for splitting.
    """
    
    def __init__(
        self,
        max_depth: Optional[int] = None,
        min_samples_split: int = 2,
        min_samples_leaf: int = 1
    ):
        self.max_depth = max_depth
        self.min_samples_split = min_samples_split
        self.min_samples_leaf = min_samples_leaf
        
        self.tree: Optional[TreeNode] = None
        self.n_features: int = 0
    
    def _variance(self, y: List[float]) -> float:
        """Compute variance."""
        if len(y) < 2:
            return 0.0
        
        mean = sum(y) / len(y)
        return sum((yi - mean) ** 2 for yi in y) / len(y)
    
    def _variance_reduction(
        self,
        y: List[float],
        y_left: List[float],
        y_right: List[float]
    ) -> float:
        """Compute variance reduction from split."""
        n = len(y)
        n_left = len(y_left)
        n_right = len(y_right)
        
        if n_left == 0 or n_right == 0:
            return 0.0
        
        parent_var = self._variance(y)
        left_var = self._variance(y_left)
        right_var = self._variance(y_right)
        
        child_var = (n_left / n) * left_var + (n_right / n) * right_var
        
        return parent_var - child_var
    
    def _best_split(
        self,
        X: List[List[float]],
        y: List[float]
    ) -> Tuple[Optional[int], Optional[float], float]:
        """Find best split for regression."""
        best_reduction = 0.0
        best_feature = None
        best_threshold = None
        
        for feature in range(self.n_features):
            values = sorted(set(X[i][feature] for i in range(len(X))))
            
            thresholds = [
                (values[i] + values[i + 1]) / 2
                for i in range(len(values) - 1)
            ]
            
            for threshold in thresholds:
                left_indices = [i for i in range(len(X)) if X[i][feature] <= threshold]
                right_indices = [i for i in range(len(X)) if X[i][feature] > threshold]
                
                if (len(left_indices) < self.min_samples_leaf or
                    len(right_indices) < self.min_samples_leaf):
                    continue
                
                y_left = [y[i] for i in left_indices]
                y_right = [y[i] for i in right_indices]
                
                reduction = self._variance_reduction(y, y_left, y_right)
                
                if reduction > best_reduction:
                    best_reduction = reduction
                    best_feature = feature
                    best_threshold = threshold
        
        return best_feature, best_threshold, best_reduction
    
    def _build_tree(
        self,
        X: List[List[float]],
        y: List[float],
        depth: int
    ) -> TreeNode:
        """Build regression tree."""
        n_samples = len(y)
        
        node = TreeNode(
            n_samples=n_samples,
            impurity=self._variance(y)
        )
        
        # Stopping criteria
        if (n_samples < self.min_samples_split or
            (self.max_depth is not None and depth >= self.max_depth) or
            self._variance(y) == 0):
            node.value = sum(y) / len(y)
            return node
        
        best_feature, best_threshold, best_reduction = self._best_split(X, y)
        
        if best_reduction == 0:
            node.value = sum(y) / len(y)
            return node
        
        left_indices = [i for i in range(len(X)) if X[i][best_feature] <= best_threshold]
        right_indices = [i for i in range(len(X)) if X[i][best_feature] > best_threshold]
        
        X_left = [X[i] for i in left_indices]
        y_left = [y[i] for i in left_indices]
        X_right = [X[i] for i in right_indices]
        y_right = [y[i] for i in right_indices]
        
        node.feature = best_feature
        node.threshold = best_threshold
        node.left = self._build_tree(X_left, y_left, depth + 1)
        node.right = self._build_tree(X_right, y_right, depth + 1)
        
        return node
    
    def fit(
        self,
        X: List[List[float]],
        y: List[float]
    ) -> 'DecisionTreeRegressor':
        """
        Build regression tree.
        
        >>> dt = DecisionTreeRegressor(max_depth=2)
        >>> X = [[1], [2], [3], [4], [5]]
        >>> y = [1.0, 2.0, 3.0, 10.0, 11.0]
        >>> dt.fit(X, y)  # doctest: +ELLIPSIS
        <...DecisionTreeRegressor object at ...>
        """
        self.n_features = len(X[0])
        self.tree = self._build_tree(X, y, depth=0)
        return self
    
    def predict(self, X: List[List[float]]) -> List[float]:
        """Predict values."""
        def _predict_single(x: List[float], node: TreeNode) -> float:
            if node.value is not None:
                return node.value
            if x[node.feature] <= node.threshold:
                return _predict_single(x, node.left)
            return _predict_single(x, node.right)
        
        return [_predict_single(x, self.tree) for x in X]


def prune_tree(
    tree: TreeNode,
    X_val: List[List[float]],
    y_val: List[int],
    classes: List[Any]
) -> TreeNode:
    """
    Post-prune tree using validation set (Reduced Error Pruning).
    """
    def _accuracy(node: TreeNode, X: List[List[float]], y: List[int]) -> float:
        """Compute accuracy with current tree."""
        if not X:
            return 1.0
        
        correct = 0
        for x, yi in zip(X, y):
            pred_node = node
            while pred_node.value is None:
                if x[pred_node.feature] <= pred_node.threshold:
                    pred_node = pred_node.left
                else:
                    pred_node = pred_node.right
            if pred_node.value == yi:
                correct += 1
        
        return correct / len(y)
    
    def _prune(node: TreeNode, X: List[List[float]], y: List[int]) -> TreeNode:
        if node.value is not None:
            return node
        
        # Split validation data
        left_indices = [i for i in range(len(X)) if X[i][node.feature] <= node.threshold]
        right_indices = [i for i in range(len(X)) if X[i][node.feature] > node.threshold]
        
        X_left = [X[i] for i in left_indices]
        y_left = [y[i] for i in left_indices]
        X_right = [X[i] for i in right_indices]
        y_right = [y[i] for i in right_indices]
        
        # Recursively prune children
        node.left = _prune(node.left, X_left, y_left)
        node.right = _prune(node.right, X_right, y_right)
        
        # Try pruning this node
        if node.left.value is not None and node.right.value is not None:
            # Compute accuracy before pruning
            acc_before = _accuracy(node, X, y)
            
            # Compute accuracy after pruning (use majority class)
            majority_class = Counter(y).most_common(1)[0][0] if y else 0
            acc_after = sum(1 for yi in y if yi == majority_class) / len(y) if y else 1.0
            
            if acc_after >= acc_before:
                # Prune
                return TreeNode(value=majority_class, n_samples=len(y))
        
        return node
    
    return _prune(tree, X_val, y_val)
```

## 7. Applications

### 7.1 Classic Applications

- Medical diagnosis
- Customer churn prediction
- Credit risk assessment
- Fraud detection
- Species classification

### 7.2 Advantages

| Advantage | Description |
|-----------|-------------|
| Interpretable | Can visualize and explain decisions |
| No scaling needed | Handles raw features |
| Handles mixed data | Numeric and categorical |
| Feature selection | Implicitly selects important features |

### 7.3 Disadvantages

| Disadvantage | Solution |
|--------------|----------|
| Overfitting | Pruning, ensemble methods |
| Unstable | Random forests, bagging |
| Axis-aligned only | Oblique trees |
| Bias to many-valued | Gain ratio |

## 8. Real-World Software Engineering Applications

### 8.1 Production Example: Diagnostic System

```python
from typing import List, Dict, Tuple, Optional
import json


class DiagnosticDecisionTree:
    """
    Medical diagnostic system using decision tree.
    """
    
    def __init__(self, max_depth: int = 5):
        self.tree = DecisionTreeClassifier(
            max_depth=max_depth,
            min_samples_split=10,
            criterion='entropy'
        )
        self.feature_names: List[str] = []
        self.symptom_descriptions: Dict[str, str] = {}
    
    def fit(
        self,
        patient_records: List[Dict],
        diagnoses: List[str],
        symptom_descriptions: Optional[Dict[str, str]] = None
    ) -> 'DiagnosticDecisionTree':
        """
        Train diagnostic tree from patient records.
        
        >>> diag = DiagnosticDecisionTree(max_depth=3)
        >>> records = [
        ...     {'fever': 1, 'cough': 1, 'fatigue': 0},
        ...     {'fever': 1, 'cough': 0, 'fatigue': 1},
        ...     {'fever': 0, 'cough': 1, 'fatigue': 0},
        ...     {'fever': 0, 'cough': 0, 'fatigue': 0}
        ... ]
        >>> diagnoses = ['flu', 'covid', 'cold', 'healthy']
        >>> diag.fit(records, diagnoses)  # doctest: +ELLIPSIS
        <...DiagnosticDecisionTree object at ...>
        """
        if not patient_records:
            return self
        
        self.feature_names = list(patient_records[0].keys())
        self.symptom_descriptions = symptom_descriptions or {}
        
        # Convert to matrix
        X = [[record[f] for f in self.feature_names] for record in patient_records]
        
        self.tree.fit(X, diagnoses)
        return self
    
    def diagnose(
        self,
        symptoms: Dict[str, float]
    ) -> Tuple[str, List[str]]:
        """
        Diagnose based on symptoms.
        
        Returns (diagnosis, reasoning_path).
        """
        x = [symptoms.get(f, 0) for f in self.feature_names]
        
        # Get diagnosis
        diagnosis = self.tree.predict([x])[0]
        
        # Build reasoning path
        reasoning = []
        node = self.tree.tree
        
        while node.value is None:
            feature_name = self.feature_names[node.feature]
            feature_value = x[node.feature]
            
            if feature_value <= node.threshold:
                direction = "No" if node.threshold == 0.5 else f"≤ {node.threshold}"
                reasoning.append(f"{feature_name}: {direction}")
                node = node.left
            else:
                direction = "Yes" if node.threshold == 0.5 else f"> {node.threshold}"
                reasoning.append(f"{feature_name}: {direction}")
                node = node.right
        
        return diagnosis, reasoning
    
    def get_decision_rules(self) -> List[str]:
        """Extract decision rules from tree."""
        rules = []
        
        def _extract_rules(
            node: TreeNode,
            path: List[str],
            depth: int
        ):
            if node.value is not None:
                condition = " AND ".join(path) if path else "TRUE"
                diagnosis = self.tree.classes[node.value]
                rules.append(f"IF {condition} THEN {diagnosis}")
                return
            
            feature_name = self.feature_names[node.feature]
            
            # Left branch
            left_cond = f"{feature_name} ≤ {node.threshold}"
            _extract_rules(node.left, path + [left_cond], depth + 1)
            
            # Right branch
            right_cond = f"{feature_name} > {node.threshold}"
            _extract_rules(node.right, path + [right_cond], depth + 1)
        
        _extract_rules(self.tree.tree, [], 0)
        return rules
    
    def export_to_json(self) -> str:
        """Export tree structure as JSON for visualization."""
        def _node_to_dict(node: TreeNode) -> Dict:
            if node.value is not None:
                return {
                    'type': 'leaf',
                    'prediction': self.tree.classes[node.value],
                    'samples': node.n_samples
                }
            
            return {
                'type': 'split',
                'feature': self.feature_names[node.feature],
                'threshold': node.threshold,
                'impurity': node.impurity,
                'samples': node.n_samples,
                'left': _node_to_dict(node.left),
                'right': _node_to_dict(node.right)
            }
        
        return json.dumps(_node_to_dict(self.tree.tree), indent=2)


class FraudDetectionTree:
    """
    Fraud detection using interpretable decision tree.
    """
    
    def __init__(self, max_depth: int = 6):
        self.tree = DecisionTreeClassifier(
            max_depth=max_depth,
            min_samples_split=50,
            criterion='gini'
        )
        self.feature_names: List[str] = []
        self.thresholds: Dict[str, Tuple[float, float]] = {}
    
    def _preprocess(
        self,
        transactions: List[Dict],
        fit: bool = True
    ) -> List[List[float]]:
        """Extract and normalize features."""
        if not transactions:
            return []
        
        if fit:
            self.feature_names = [
                'amount', 'hour', 'is_weekend', 'country_mismatch',
                'device_age_days', 'transactions_last_hour'
            ]
            
            # Compute thresholds for binning
            amounts = [t.get('amount', 0) for t in transactions]
            self.thresholds['amount'] = (
                sum(amounts) / len(amounts),
                max(amounts)
            )
        
        features = []
        for t in transactions:
            row = [
                t.get('amount', 0) / self.thresholds['amount'][1],
                t.get('hour', 12) / 24,
                1.0 if t.get('is_weekend', False) else 0.0,
                1.0 if t.get('country_mismatch', False) else 0.0,
                min(t.get('device_age_days', 365), 365) / 365,
                min(t.get('transactions_last_hour', 0), 10) / 10
            ]
            features.append(row)
        
        return features
    
    def fit(
        self,
        transactions: List[Dict],
        is_fraud: List[int]
    ) -> 'FraudDetectionTree':
        """
        Train fraud detection model.
        
        >>> fd = FraudDetectionTree(max_depth=3)
        >>> transactions = [
        ...     {'amount': 100, 'hour': 14, 'country_mismatch': False},
        ...     {'amount': 5000, 'hour': 3, 'country_mismatch': True},
        ...     {'amount': 50, 'hour': 10, 'country_mismatch': False},
        ...     {'amount': 3000, 'hour': 2, 'country_mismatch': True}
        ... ]
        >>> is_fraud = [0, 1, 0, 1]
        >>> fd.fit(transactions, is_fraud)  # doctest: +ELLIPSIS
        <...FraudDetectionTree object at ...>
        """
        X = self._preprocess(transactions, fit=True)
        self.tree.fit(X, is_fraud)
        return self
    
    def predict(
        self,
        transactions: List[Dict]
    ) -> List[Tuple[int, float, str]]:
        """
        Predict fraud with explanation.
        
        Returns list of (is_fraud, confidence, reason).
        """
        X = self._preprocess(transactions, fit=False)
        predictions = self.tree.predict(X)
        probas = self.tree.predict_proba(X)
        
        results = []
        for i, (x, pred) in enumerate(zip(X, predictions)):
            # Get confidence
            proba = probas[i][1] if pred == 1 else 1 - probas[i][0]
            
            # Generate explanation
            reasons = []
            node = self.tree.tree
            
            while node.value is None:
                feature_name = self.feature_names[node.feature]
                feature_value = x[node.feature]
                
                if feature_value <= node.threshold:
                    node = node.left
                else:
                    if feature_name == 'amount' and feature_value > 0.5:
                        reasons.append("High transaction amount")
                    elif feature_name == 'hour' and (feature_value < 0.25 or feature_value > 0.92):
                        reasons.append("Unusual hour")
                    elif feature_name == 'country_mismatch' and feature_value > 0.5:
                        reasons.append("Country mismatch")
                    node = node.right
            
            reason = "; ".join(reasons) if reasons else "Normal pattern"
            results.append((pred, proba, reason))
        
        return results
    
    def get_fraud_rules(self) -> List[Dict]:
        """
        Get rules that lead to fraud prediction.
        """
        fraud_rules = []
        
        def _extract(node: TreeNode, conditions: List[Dict]):
            if node.value is not None:
                if self.tree.classes[node.value] == 1:
                    fraud_rules.append({
                        'conditions': conditions.copy(),
                        'samples': node.n_samples
                    })
                return
            
            feature_name = self.feature_names[node.feature]
            
            _extract(node.left, conditions + [{
                'feature': feature_name,
                'operator': '<=',
                'threshold': node.threshold
            }])
            
            _extract(node.right, conditions + [{
                'feature': feature_name,
                'operator': '>',
                'threshold': node.threshold
            }])
        
        _extract(self.tree.tree, [])
        return fraud_rules


# Demo
if __name__ == "__main__":
    print("Diagnostic Decision Tree Demo")
    print("=" * 50)
    
    # Training data
    patient_records = [
        {'fever': 1, 'cough': 1, 'sore_throat': 0, 'runny_nose': 0, 'body_ache': 1},
        {'fever': 1, 'cough': 0, 'sore_throat': 1, 'runny_nose': 0, 'body_ache': 1},
        {'fever': 0, 'cough': 1, 'sore_throat': 1, 'runny_nose': 1, 'body_ache': 0},
        {'fever': 0, 'cough': 0, 'sore_throat': 1, 'runny_nose': 1, 'body_ache': 0},
        {'fever': 1, 'cough': 1, 'sore_throat': 0, 'runny_nose': 0, 'body_ache': 0},
        {'fever': 0, 'cough': 0, 'sore_throat': 0, 'runny_nose': 0, 'body_ache': 0},
    ]
    diagnoses = ['flu', 'covid', 'cold', 'allergy', 'flu', 'healthy']
    
    diag = DiagnosticDecisionTree(max_depth=4)
    diag.fit(patient_records, diagnoses)
    
    # Test diagnosis
    test_symptoms = {'fever': 1, 'cough': 1, 'sore_throat': 0, 'runny_nose': 0, 'body_ache': 0}
    diagnosis, reasoning = diag.diagnose(test_symptoms)
    
    print(f"Symptoms: {test_symptoms}")
    print(f"Diagnosis: {diagnosis}")
    print(f"Reasoning: {' → '.join(reasoning)}")
    
    print("\nDecision Rules:")
    for rule in diag.get_decision_rules()[:3]:
        print(f"  {rule}")
    
    print("\n" + "=" * 50)
    print("Fraud Detection Demo")
    print("=" * 50)
    
    # Training data
    import random
    random.seed(42)
    
    transactions = []
    is_fraud = []
    
    # Normal transactions
    for _ in range(100):
        transactions.append({
            'amount': random.gauss(200, 100),
            'hour': random.randint(8, 20),
            'is_weekend': random.random() < 0.3,
            'country_mismatch': False,
            'device_age_days': random.randint(30, 365),
            'transactions_last_hour': random.randint(0, 3)
        })
        is_fraud.append(0)
    
    # Fraudulent transactions
    for _ in range(20):
        transactions.append({
            'amount': random.gauss(2000, 500),
            'hour': random.choice([1, 2, 3, 4, 23]),
            'is_weekend': random.random() < 0.5,
            'country_mismatch': random.random() < 0.7,
            'device_age_days': random.randint(0, 7),
            'transactions_last_hour': random.randint(5, 10)
        })
        is_fraud.append(1)
    
    fd = FraudDetectionTree(max_depth=4)
    fd.fit(transactions, is_fraud)
    
    # Test transactions
    test_transactions = [
        {'amount': 150, 'hour': 14, 'country_mismatch': False, 'device_age_days': 100, 'transactions_last_hour': 1},
        {'amount': 3000, 'hour': 2, 'country_mismatch': True, 'device_age_days': 2, 'transactions_last_hour': 8}
    ]
    
    results = fd.predict(test_transactions)
    for t, (pred, conf, reason) in zip(test_transactions, results):
        status = "FRAUD" if pred == 1 else "LEGIT"
        print(f"\nTransaction: ${t['amount']:.0f} at {t['hour']}:00")
        print(f"  Status: {status} (confidence: {conf:.1%})")
        print(f"  Reason: {reason}")
    
    print("\nFraud Detection Rules:")
    for rule in fd.get_fraud_rules()[:2]:
        print(f"  Conditions: {rule['conditions']}")
        print(f"  Samples: {rule['samples']}")
```

## 9. Ensemble Methods

Decision trees are building blocks for powerful ensembles:

| Method | Description | Bias-Variance |
|--------|-------------|---------------|
| Random Forest | Bagging + random features | ↓Variance |
| Gradient Boosting | Sequential correction | ↓Bias |
| AdaBoost | Weighted resampling | ↓Bias |
| Extra Trees | Random splits | ↓Variance |

## 10. Edge Cases and Handling

| Edge Case | Handling |
|-----------|----------|
| Missing values | Surrogate splits |
| Imbalanced classes | Class weights |
| High cardinality categorical | Grouping, target encoding |
| Continuous target | Regression tree |
| Multiclass | One-vs-rest or native |

## 11. References

- [Wikipedia: Decision Tree Learning](https://en.wikipedia.org/wiki/Decision_tree_learning)
- Breiman, L. et al. "Classification and Regression Trees" (1984)
- Quinlan, J.R. "C4.5: Programs for Machine Learning" (1993)
