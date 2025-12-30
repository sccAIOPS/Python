# Boosting Algorithms

## Overview
- **Category**: Ensemble Learning / Classification & Regression
- **Complexity**: AdaBoost: O(T×n×d) | Gradient Boosting: O(T×n×d×log n)
- **Type**: Sequential ensemble that converts weak learners to strong learners
- **Source Files**: [machine_learning/gradient_boosting.py](../../../machine_learning/gradient_boosting.py)

## 1. Mathematical Foundation

### 1.1 Boosting Principle

Boosting creates a strong classifier by combining weak classifiers:

$$
H(\mathbf{x}) = \text{sign}\left(\sum_{t=1}^{T}\alpha_t h_t(\mathbf{x})\right)
$$

where $h_t$ = weak learners, $\alpha_t$ = learner weights.

### 1.2 AdaBoost (Adaptive Boosting)

**Sample weight update**:
$$
D_{t+1}(i) = \frac{D_t(i) \exp(-\alpha_t y_i h_t(x_i))}{Z_t}
$$

**Learner weight**:
$$
\alpha_t = \frac{1}{2}\ln\left(\frac{1 - \epsilon_t}{\epsilon_t}\right)
$$

**Weighted error**:
$$
\epsilon_t = \sum_{i: h_t(x_i) \neq y_i} D_t(i)
$$

### 1.3 Gradient Boosting

**Objective**: Minimize loss function $L(y, F(\mathbf{x}))$

**Additive model**:
$$
F_m(\mathbf{x}) = F_{m-1}(\mathbf{x}) + \gamma_m h_m(\mathbf{x})
$$

**Pseudo-residuals** (negative gradient):
$$
r_{im} = -\left[\frac{\partial L(y_i, F(x_i))}{\partial F(x_i)}\right]_{F=F_{m-1}}
$$

### 1.4 Common Loss Functions

**Regression (MSE)**:
$$
L(y, F) = \frac{1}{2}(y - F)^2, \quad r_i = y_i - F(x_i)
$$

**Classification (Log loss)**:
$$
L(y, F) = \log(1 + e^{-yF}), \quad r_i = \frac{y_i}{1 + e^{y_i F(x_i)}}
$$

**Huber loss** (robust):
$$
L(y, F) = \begin{cases}
\frac{1}{2}(y-F)^2 & |y-F| \leq \delta \\
\delta|y-F| - \frac{\delta^2}{2} & |y-F| > \delta
\end{cases}
$$

### 1.5 Learning Rate (Shrinkage)

$$
F_m(\mathbf{x}) = F_{m-1}(\mathbf{x}) + \nu \cdot \gamma_m h_m(\mathbf{x})
$$

where $\nu \in (0, 1]$ is the learning rate.

### 1.6 XGBoost Objective

**Regularized objective**:
$$
\mathcal{L}(\phi) = \sum_i L(y_i, \hat{y}_i) + \sum_k \Omega(f_k)
$$

**Regularization term**:
$$
\Omega(f) = \gamma T + \frac{1}{2}\lambda\sum_{j=1}^{T}w_j^2
$$

**Second-order Taylor expansion**:
$$
\mathcal{L}^{(t)} \approx \sum_i [g_i f_t(x_i) + \frac{1}{2}h_i f_t^2(x_i)] + \Omega(f_t)
$$

where $g_i = \partial_{\hat{y}} L$, $h_i = \partial^2_{\hat{y}} L$.

### 1.7 Optimal Leaf Weights

$$
w_j^* = -\frac{\sum_{i \in I_j} g_i}{\sum_{i \in I_j} h_i + \lambda}
$$

**Split gain**:
$$
\text{Gain} = \frac{1}{2}\left[\frac{G_L^2}{H_L + \lambda} + \frac{G_R^2}{H_R + \lambda} - \frac{(G_L + G_R)^2}{H_L + H_R + \lambda}\right] - \gamma
$$

## 2. Pseudocode

### 2.1 AdaBoost

```
ALGORITHM AdaBoost(X, y, T)
    INPUT: Data X, labels y ∈ {-1, +1}, number of rounds T
    OUTPUT: Final classifier H
    
    n ← |X|
    D₁ ← uniform distribution (1/n for all samples)
    
    for t ← 1 to T do
        // Train weak learner with sample weights
        hₜ ← TrainWeakLearner(X, y, Dₜ)
        
        // Compute weighted error
        εₜ ← Σᵢ Dₜ(i) × 𝟙[hₜ(xᵢ) ≠ yᵢ]
        
        // Stop if error too high
        if εₜ ≥ 0.5 then
            T ← t - 1
            break
        
        // Compute learner weight
        αₜ ← 0.5 × ln((1 - εₜ) / εₜ)
        
        // Update sample weights
        for i ← 1 to n do
            Dₜ₊₁(i) ← Dₜ(i) × exp(-αₜ × yᵢ × hₜ(xᵢ))
        
        // Normalize
        Dₜ₊₁ ← Dₜ₊₁ / Σⱼ Dₜ₊₁(j)
    
    // Final classifier
    H(x) ← sign(Σₜ αₜ × hₜ(x))
    
    return H

ALGORITHM PredictAdaBoost(H, x)
    INPUT: Ensemble H = {(h₁, α₁), ..., (hₜ, αₜ)}, query x
    OUTPUT: Predicted class
    
    score ← 0
    for (h, α) in H do
        score ← score + α × h(x)
    
    return sign(score)
```

### 2.2 Gradient Boosting

```
ALGORITHM GradientBoosting(X, y, T, ν)
    INPUT: Data X, labels y, iterations T, learning rate ν
    OUTPUT: Boosted model F
    
    // Initialize with constant
    F₀(x) ← argmin_γ Σᵢ L(yᵢ, γ)
    
    for m ← 1 to T do
        // Compute pseudo-residuals (negative gradient)
        for i ← 1 to n do
            rᵢₘ ← -[∂L(yᵢ, F(xᵢ)) / ∂F(xᵢ)]_{F=Fₘ₋₁}
        
        // Fit weak learner to pseudo-residuals
        hₘ ← FitTree(X, r)
        
        // Find optimal step size for each leaf
        for each leaf j in hₘ do
            γⱼₘ ← argmin_γ Σᵢ∈Rⱼₘ L(yᵢ, Fₘ₋₁(xᵢ) + γ)
        
        // Update model
        Fₘ(x) ← Fₘ₋₁(x) + ν × hₘ(x)
    
    return Fₜ

ALGORITHM ComputeResiduals(y, F, loss_type)
    INPUT: True values y, predictions F, loss type
    OUTPUT: Pseudo-residuals
    
    if loss_type = "mse" then
        residuals ← y - F
    else if loss_type = "log_loss" then
        p ← sigmoid(F)
        residuals ← y - p
    else if loss_type = "huber" then
        diff ← y - F
        residuals ← diff where |diff| ≤ δ else δ × sign(diff)
    
    return residuals
```

### 2.3 XGBoost Split Finding

```
ALGORITHM FindBestSplit(X, g, h, λ, γ)
    INPUT: Feature values X, gradients g, hessians h, regularization
    OUTPUT: Best split
    
    G ← Σᵢ gᵢ
    H ← Σᵢ hᵢ
    
    best_gain ← 0
    best_feature, best_threshold ← None, None
    
    for each feature f do
        // Sort by feature value
        Sort samples by X[:, f]
        
        G_L ← 0, H_L ← 0
        
        for each split point j do
            G_L ← G_L + g[j]
            H_L ← H_L + h[j]
            G_R ← G - G_L
            H_R ← H - H_L
            
            gain ← (G_L² / (H_L + λ) + G_R² / (H_R + λ) - G² / (H + λ)) / 2 - γ
            
            if gain > best_gain then
                best_gain ← gain
                best_feature ← f
                best_threshold ← X[j, f]
    
    return best_feature, best_threshold, best_gain
```

## 3. Step-by-Step Example

### AdaBoost Example

**Data** (5 samples):
| i | X | y |
|---|---|---|
| 1 | 1 | +1 |
| 2 | 2 | +1 |
| 3 | 3 | -1 |
| 4 | 4 | -1 |
| 5 | 5 | -1 |

**Round 1**:
- D₁ = [0.2, 0.2, 0.2, 0.2, 0.2]
- h₁: X ≤ 2.5 → +1, else -1
- Error: ε₁ = 0 (perfect)
- α₁ = ∞ (capped at large value)

**Round 2**:
- h₂ refines boundary
- Misclassified samples get higher weight

**Final**: H(x) = sign(α₁h₁(x) + α₂h₂(x))

## 4. Complexity Analysis

| Algorithm | Training | Prediction | Space |
|-----------|----------|------------|-------|
| AdaBoost | O(T×n×d) | O(T×d) | O(T×d) |
| Gradient Boosting | O(T×n×d×log n) | O(T×log n) | O(T×nodes) |
| XGBoost | O(T×n×d) | O(T×log n) | O(T×nodes) |

Where T = iterations, n = samples, d = features.

## 5. Visual Representation

### 5.1 AdaBoost Weight Evolution

```
Round 1: Equal weights
┌─┬─┬─┬─┬─┐
│●│●│○│○│○│  D = [0.2, 0.2, 0.2, 0.2, 0.2]
└─┴─┴─┴─┴─┘
 + + - - -   (● = +1, ○ = -1)

Round 2: After h₁ misclassifies sample 3
┌─┬─┬───┬─┬─┐
│ │ │███│ │ │  D = [0.1, 0.1, 0.4, 0.2, 0.2]
└─┴─┴───┴─┴─┘
         ↑ Weight increased

Round 3: After h₂ misclassifies sample 1
┌───┬─┬─┬─┬─┐
│███│ │ │ │ │  D = [0.3, 0.1, 0.2, 0.2, 0.2]
└───┴─┴─┴─┴─┘
```

### 5.2 Gradient Boosting Residual Fitting

```
Original Target: [3, 5, 7, 9]

Round 0: F₀(x) = mean = 6
Residuals:     [-3, -1, +1, +3]

Round 1: h₁ fits residuals
         Prediction adjustment: [-2, -1, +1, +2]
         F₁ = F₀ + 0.1×h₁
         New residuals: [-2.8, -0.9, +0.9, +2.8]

Round 2: h₂ fits new residuals
         ...continuing to reduce error
```

### 5.3 Boosting vs Bagging

```
Bagging (Random Forest):
┌────────────┐ ┌────────────┐ ┌────────────┐
│   Tree 1   │ │   Tree 2   │ │   Tree 3   │
│  (parallel)│ │  (parallel)│ │  (parallel)│
└──────┬─────┘ └──────┬─────┘ └──────┬─────┘
       │              │              │
       └──────────────┼──────────────┘
                      ▼
               Average/Vote

Boosting:
┌────────────┐
│   h₁       │──── Error analysis
└──────┬─────┘           │
       │                 ▼
┌──────▼─────┐
│   h₂       │──── Focus on h₁ errors
└──────┬─────┘           │
       │                 ▼
┌──────▼─────┐
│   h₃       │──── Focus on h₁+h₂ errors
└──────┬─────┘
       ▼
  H = Σ αₜhₜ
```

## 6. Implementation

```python
from typing import List, Tuple, Optional, Dict, Callable
import math
import random
from collections import Counter


class DecisionStump:
    """
    Decision stump - tree with single split.
    Used as weak learner in AdaBoost.
    """
    
    def __init__(self):
        self.feature: int = 0
        self.threshold: float = 0.0
        self.polarity: int = 1  # 1 or -1
    
    def fit(
        self,
        X: List[List[float]],
        y: List[int],
        weights: List[float]
    ) -> 'DecisionStump':
        """
        Train decision stump with sample weights.
        
        >>> stump = DecisionStump()
        >>> X = [[1], [2], [3], [4]]
        >>> y = [1, 1, -1, -1]
        >>> w = [0.25, 0.25, 0.25, 0.25]
        >>> stump.fit(X, y, w) is not None
        True
        """
        n_samples = len(X)
        n_features = len(X[0])
        
        best_error = float('inf')
        
        for feature in range(n_features):
            values = sorted(set(X[i][feature] for i in range(n_samples)))
            
            for i in range(len(values) - 1):
                threshold = (values[i] + values[i + 1]) / 2
                
                for polarity in [1, -1]:
                    # Predictions: polarity * (1 if x <= threshold else -1)
                    error = 0.0
                    
                    for j in range(n_samples):
                        pred = polarity * (1 if X[j][feature] <= threshold else -1)
                        if pred != y[j]:
                            error += weights[j]
                    
                    if error < best_error:
                        best_error = error
                        self.feature = feature
                        self.threshold = threshold
                        self.polarity = polarity
        
        return self
    
    def predict(self, X: List[List[float]]) -> List[int]:
        """Predict class labels."""
        predictions = []
        for x in X:
            if x[self.feature] <= self.threshold:
                predictions.append(self.polarity)
            else:
                predictions.append(-self.polarity)
        return predictions


class AdaBoostClassifier:
    """
    AdaBoost classifier.
    
    Combines weak learners (decision stumps) with weighted voting.
    """
    
    def __init__(
        self,
        n_estimators: int = 50,
        learning_rate: float = 1.0,
        random_state: Optional[int] = None
    ):
        """
        Initialize AdaBoost.
        
        Args:
            n_estimators: Number of boosting rounds
            learning_rate: Contribution of each classifier
            random_state: Random seed
        """
        self.n_estimators = n_estimators
        self.learning_rate = learning_rate
        
        if random_state is not None:
            random.seed(random_state)
        
        self.estimators: List[DecisionStump] = []
        self.estimator_weights: List[float] = []
        self.estimator_errors: List[float] = []
    
    def fit(
        self,
        X: List[List[float]],
        y: List[int]
    ) -> 'AdaBoostClassifier':
        """
        Train AdaBoost classifier.
        
        >>> ada = AdaBoostClassifier(n_estimators=5, random_state=42)
        >>> X = [[1, 2], [2, 3], [3, 4], [6, 7], [7, 8], [8, 9]]
        >>> y = [1, 1, 1, -1, -1, -1]
        >>> ada.fit(X, y) is not None
        True
        """
        n_samples = len(X)
        
        # Ensure labels are -1 or +1
        y = [1 if yi > 0 else -1 for yi in y]
        
        # Initialize weights
        weights = [1.0 / n_samples] * n_samples
        
        self.estimators = []
        self.estimator_weights = []
        self.estimator_errors = []
        
        for t in range(self.n_estimators):
            # Train weak learner
            stump = DecisionStump()
            stump.fit(X, y, weights)
            
            # Get predictions
            predictions = stump.predict(X)
            
            # Compute weighted error
            error = sum(
                w for w, pred, yi in zip(weights, predictions, y)
                if pred != yi
            )
            
            # Avoid numerical issues
            error = max(error, 1e-10)
            error = min(error, 1 - 1e-10)
            
            # Stop if error too high
            if error >= 0.5:
                if t == 0:
                    # At least one estimator
                    self.estimators.append(stump)
                    self.estimator_weights.append(0.0)
                break
            
            # Compute estimator weight
            alpha = self.learning_rate * 0.5 * math.log((1 - error) / error)
            
            # Update sample weights
            for i in range(n_samples):
                weights[i] *= math.exp(-alpha * y[i] * predictions[i])
            
            # Normalize weights
            weight_sum = sum(weights)
            weights = [w / weight_sum for w in weights]
            
            # Store estimator
            self.estimators.append(stump)
            self.estimator_weights.append(alpha)
            self.estimator_errors.append(error)
        
        return self
    
    def decision_function(self, X: List[List[float]]) -> List[float]:
        """Compute weighted sum of predictions."""
        scores = [0.0] * len(X)
        
        for stump, alpha in zip(self.estimators, self.estimator_weights):
            predictions = stump.predict(X)
            for i, pred in enumerate(predictions):
                scores[i] += alpha * pred
        
        return scores
    
    def predict(self, X: List[List[float]]) -> List[int]:
        """
        Predict class labels.
        
        >>> ada = AdaBoostClassifier(n_estimators=10)
        >>> ada.fit([[1], [2], [5], [6]], [1, 1, -1, -1])
        <...>
        >>> ada.predict([[1.5], [5.5]])
        [1, -1]
        """
        scores = self.decision_function(X)
        return [1 if s >= 0 else -1 for s in scores]
    
    def score(self, X: List[List[float]], y: List[int]) -> float:
        """Compute accuracy."""
        y = [1 if yi > 0 else -1 for yi in y]
        predictions = self.predict(X)
        correct = sum(1 for p, yi in zip(predictions, y) if p == yi)
        return correct / len(y)


class GradientBoostingTree:
    """Regression tree for gradient boosting."""
    
    def __init__(self, max_depth: int = 3, min_samples_leaf: int = 1):
        self.max_depth = max_depth
        self.min_samples_leaf = min_samples_leaf
        self.root = None
    
    def _build_tree(
        self,
        X: List[List[float]],
        residuals: List[float],
        depth: int
    ) -> dict:
        """Build regression tree."""
        n = len(residuals)
        
        # Leaf node conditions
        if depth >= self.max_depth or n < 2 * self.min_samples_leaf:
            return {'value': sum(residuals) / n if n > 0 else 0}
        
        # Find best split
        best_feature, best_threshold, best_gain = None, None, -float('inf')
        
        n_features = len(X[0])
        for feature in range(n_features):
            values = sorted(set(X[i][feature] for i in range(n)))
            
            for i in range(len(values) - 1):
                threshold = (values[i] + values[i + 1]) / 2
                
                left_idx = [j for j in range(n) if X[j][feature] <= threshold]
                right_idx = [j for j in range(n) if X[j][feature] > threshold]
                
                if len(left_idx) < self.min_samples_leaf or len(right_idx) < self.min_samples_leaf:
                    continue
                
                left_res = [residuals[j] for j in left_idx]
                right_res = [residuals[j] for j in right_idx]
                
                # Variance reduction
                var_before = sum((r - sum(residuals)/n)**2 for r in residuals)
                var_left = sum((r - sum(left_res)/len(left_res))**2 for r in left_res)
                var_right = sum((r - sum(right_res)/len(right_res))**2 for r in right_res)
                
                gain = var_before - var_left - var_right
                
                if gain > best_gain:
                    best_gain = gain
                    best_feature = feature
                    best_threshold = threshold
        
        if best_feature is None:
            return {'value': sum(residuals) / n if n > 0 else 0}
        
        # Split data
        left_idx = [i for i in range(n) if X[i][best_feature] <= best_threshold]
        right_idx = [i for i in range(n) if X[i][best_feature] > best_threshold]
        
        return {
            'feature': best_feature,
            'threshold': best_threshold,
            'left': self._build_tree(
                [X[i] for i in left_idx],
                [residuals[i] for i in left_idx],
                depth + 1
            ),
            'right': self._build_tree(
                [X[i] for i in right_idx],
                [residuals[i] for i in right_idx],
                depth + 1
            )
        }
    
    def fit(self, X: List[List[float]], residuals: List[float]) -> 'GradientBoostingTree':
        self.root = self._build_tree(X, residuals, 0)
        return self
    
    def _predict_single(self, x: List[float], node: dict) -> float:
        if 'value' in node:
            return node['value']
        
        if x[node['feature']] <= node['threshold']:
            return self._predict_single(x, node['left'])
        return self._predict_single(x, node['right'])
    
    def predict(self, X: List[List[float]]) -> List[float]:
        return [self._predict_single(x, self.root) for x in X]


class GradientBoostingRegressor:
    """
    Gradient Boosting for regression.
    """
    
    def __init__(
        self,
        n_estimators: int = 100,
        learning_rate: float = 0.1,
        max_depth: int = 3,
        min_samples_leaf: int = 1,
        subsample: float = 1.0,
        random_state: Optional[int] = None
    ):
        """
        Initialize Gradient Boosting.
        
        Args:
            n_estimators: Number of boosting stages
            learning_rate: Shrinkage factor
            max_depth: Maximum tree depth
            min_samples_leaf: Minimum samples in leaf
            subsample: Fraction of samples for each tree
            random_state: Random seed
        """
        self.n_estimators = n_estimators
        self.learning_rate = learning_rate
        self.max_depth = max_depth
        self.min_samples_leaf = min_samples_leaf
        self.subsample = subsample
        
        if random_state is not None:
            random.seed(random_state)
        
        self.trees: List[GradientBoostingTree] = []
        self.init_prediction: float = 0.0
    
    def fit(
        self,
        X: List[List[float]],
        y: List[float]
    ) -> 'GradientBoostingRegressor':
        """
        Train gradient boosting regressor.
        
        >>> gb = GradientBoostingRegressor(n_estimators=10, learning_rate=0.1)
        >>> X = [[1], [2], [3], [4], [5]]
        >>> y = [1.1, 2.0, 3.1, 3.9, 5.1]
        >>> gb.fit(X, y) is not None
        True
        """
        n = len(X)
        
        # Initialize with mean
        self.init_prediction = sum(y) / n
        predictions = [self.init_prediction] * n
        
        self.trees = []
        
        for _ in range(self.n_estimators):
            # Compute residuals
            residuals = [y[i] - predictions[i] for i in range(n)]
            
            # Subsample
            if self.subsample < 1.0:
                sample_size = int(n * self.subsample)
                indices = random.sample(range(n), sample_size)
                X_sample = [X[i] for i in indices]
                res_sample = [residuals[i] for i in indices]
            else:
                X_sample = X
                res_sample = residuals
            
            # Fit tree to residuals
            tree = GradientBoostingTree(
                max_depth=self.max_depth,
                min_samples_leaf=self.min_samples_leaf
            )
            tree.fit(X_sample, res_sample)
            
            # Update predictions
            tree_preds = tree.predict(X)
            predictions = [
                predictions[i] + self.learning_rate * tree_preds[i]
                for i in range(n)
            ]
            
            self.trees.append(tree)
        
        return self
    
    def predict(self, X: List[List[float]]) -> List[float]:
        """
        Predict values.
        
        >>> gb = GradientBoostingRegressor(n_estimators=10)
        >>> gb.fit([[1], [2], [3], [4]], [1, 2, 3, 4])
        <...>
        >>> preds = gb.predict([[2.5]])
        >>> 2 < preds[0] < 3.5
        True
        """
        predictions = [self.init_prediction] * len(X)
        
        for tree in self.trees:
            tree_preds = tree.predict(X)
            predictions = [
                predictions[i] + self.learning_rate * tree_preds[i]
                for i in range(len(X))
            ]
        
        return predictions
    
    def score(self, X: List[List[float]], y: List[float]) -> float:
        """Compute R² score."""
        predictions = self.predict(X)
        y_mean = sum(y) / len(y)
        
        ss_res = sum((y[i] - predictions[i])**2 for i in range(len(y)))
        ss_tot = sum((yi - y_mean)**2 for yi in y)
        
        return 1 - (ss_res / ss_tot) if ss_tot > 0 else 0


class GradientBoostingClassifier:
    """
    Gradient Boosting for binary classification.
    """
    
    def __init__(
        self,
        n_estimators: int = 100,
        learning_rate: float = 0.1,
        max_depth: int = 3,
        random_state: Optional[int] = None
    ):
        self.n_estimators = n_estimators
        self.learning_rate = learning_rate
        self.max_depth = max_depth
        
        if random_state is not None:
            random.seed(random_state)
        
        self.trees: List[GradientBoostingTree] = []
        self.init_prediction: float = 0.0
    
    @staticmethod
    def _sigmoid(x: float) -> float:
        """Sigmoid function with overflow protection."""
        if x >= 0:
            return 1 / (1 + math.exp(-x))
        else:
            exp_x = math.exp(x)
            return exp_x / (1 + exp_x)
    
    def fit(
        self,
        X: List[List[float]],
        y: List[int]
    ) -> 'GradientBoostingClassifier':
        """Train gradient boosting classifier."""
        n = len(X)
        
        # Convert labels to 0/1
        y = [1 if yi > 0 else 0 for yi in y]
        
        # Initialize with log-odds
        p = sum(y) / n
        self.init_prediction = math.log(p / (1 - p)) if 0 < p < 1 else 0
        
        # Current predictions (log-odds)
        F = [self.init_prediction] * n
        
        self.trees = []
        
        for _ in range(self.n_estimators):
            # Compute probabilities
            probs = [self._sigmoid(f) for f in F]
            
            # Compute residuals (negative gradient of log-loss)
            residuals = [y[i] - probs[i] for i in range(n)]
            
            # Fit tree to residuals
            tree = GradientBoostingTree(max_depth=self.max_depth)
            tree.fit(X, residuals)
            
            # Update predictions
            tree_preds = tree.predict(X)
            F = [F[i] + self.learning_rate * tree_preds[i] for i in range(n)]
            
            self.trees.append(tree)
        
        return self
    
    def predict_proba(self, X: List[List[float]]) -> List[List[float]]:
        """Predict class probabilities."""
        F = [self.init_prediction] * len(X)
        
        for tree in self.trees:
            tree_preds = tree.predict(X)
            F = [F[i] + self.learning_rate * tree_preds[i] for i in range(len(X))]
        
        probs = [self._sigmoid(f) for f in F]
        return [[1 - p, p] for p in probs]
    
    def predict(self, X: List[List[float]]) -> List[int]:
        """Predict class labels."""
        probs = self.predict_proba(X)
        return [1 if p[1] >= 0.5 else 0 for p in probs]
    
    def score(self, X: List[List[float]], y: List[int]) -> float:
        """Compute accuracy."""
        y = [1 if yi > 0 else 0 for yi in y]
        predictions = self.predict(X)
        correct = sum(1 for p, yi in zip(predictions, y) if p == yi)
        return correct / len(y)
```

## 7. Applications

### 7.1 Use Cases

- **Click-through rate prediction**
- **Ranking (search engines)**
- **Fraud detection**
- **Disease diagnosis**
- **Price prediction**
- **Customer conversion**
- **Time series forecasting**

### 7.2 When to Use Boosting

| Scenario | Recommendation |
|----------|---------------|
| Structured data | ✓ Excellent |
| Tabular data | ✓ Best choice |
| Need interpretability | ✓ Good with shallow trees |
| Small dataset | ✓ Works well |
| High dimensional | ✗ Use Random Forest |
| Image/text | ✗ Use Deep Learning |

## 8. Real-World Software Engineering Applications

### 8.1 Production Example: Click-Through Rate Prediction

```python
from typing import List, Dict, Tuple, Optional
import random
import math
from datetime import datetime


class CTRGradientBoost:
    """
    Production Gradient Boosting for Click-Through Rate prediction.
    
    Used in ad serving, recommendation systems, and content ranking.
    """
    
    def __init__(
        self,
        n_estimators: int = 100,
        learning_rate: float = 0.05,
        max_depth: int = 6,
        min_samples_leaf: int = 100,
        l2_regularization: float = 1.0,
        feature_fraction: float = 0.8
    ):
        """
        Initialize CTR predictor.
        
        Args:
            n_estimators: Number of boosting rounds
            learning_rate: Step size shrinkage
            max_depth: Maximum tree depth
            min_samples_leaf: Minimum samples in leaf (for stability)
            l2_regularization: L2 regularization term
            feature_fraction: Fraction of features per tree
        """
        self.n_estimators = n_estimators
        self.learning_rate = learning_rate
        self.max_depth = max_depth
        self.min_samples_leaf = min_samples_leaf
        self.l2_reg = l2_regularization
        self.feature_fraction = feature_fraction
        
        self.gb: Optional[GradientBoostingClassifier] = None
        
        # Feature engineering
        self.feature_names: List[str] = []
        self.categorical_encodings: Dict[str, Dict] = {}
        
        # Calibration
        self.calibration_a: float = 1.0
        self.calibration_b: float = 0.0
        
        # Metadata
        self.training_samples: int = 0
        self.training_time: Optional[datetime] = None
        self.positive_rate: float = 0.0
    
    def _extract_features(
        self,
        impression: dict
    ) -> Tuple[List[float], List[str]]:
        """
        Extract features from impression data.
        """
        features = []
        names = []
        
        # User features
        features.extend([
            impression.get('user_age', 30),
            1 if impression.get('user_gender') == 'M' else 0,
            impression.get('user_session_count', 0),
            math.log1p(impression.get('user_total_clicks', 0)),
            math.log1p(impression.get('user_total_impressions', 0)),
        ])
        names.extend(['user_age', 'user_is_male', 'session_count',
                     'log_total_clicks', 'log_total_impr'])
        
        # Historical CTR
        total_impr = impression.get('user_total_impressions', 1)
        total_clicks = impression.get('user_total_clicks', 0)
        user_ctr = total_clicks / max(total_impr, 1)
        features.append(user_ctr)
        names.append('user_historical_ctr')
        
        # Ad features
        features.extend([
            impression.get('ad_bid', 0),
            math.log1p(impression.get('ad_impressions', 0)),
            impression.get('ad_quality_score', 0.5),
        ])
        names.extend(['ad_bid', 'log_ad_impr', 'ad_quality'])
        
        # Historical ad CTR
        ad_impr = impression.get('ad_impressions', 1)
        ad_clicks = impression.get('ad_clicks', 0)
        ad_ctr = ad_clicks / max(ad_impr, 1)
        features.append(ad_ctr)
        names.append('ad_historical_ctr')
        
        # Context features
        hour = impression.get('hour', 12)
        day_of_week = impression.get('day_of_week', 0)
        
        # Hour encoding (cyclic)
        features.extend([
            math.sin(2 * math.pi * hour / 24),
            math.cos(2 * math.pi * hour / 24),
        ])
        names.extend(['hour_sin', 'hour_cos'])
        
        # Day encoding (cyclic)
        features.extend([
            math.sin(2 * math.pi * day_of_week / 7),
            math.cos(2 * math.pi * day_of_week / 7),
        ])
        names.extend(['day_sin', 'day_cos'])
        
        # Device features
        features.extend([
            1 if impression.get('device') == 'mobile' else 0,
            1 if impression.get('device') == 'tablet' else 0,
            1 if impression.get('device') == 'desktop' else 0,
        ])
        names.extend(['is_mobile', 'is_tablet', 'is_desktop'])
        
        # Position features
        position = impression.get('position', 1)
        features.extend([
            position,
            1.0 / position,  # Position bias correction
            math.log1p(position),
        ])
        names.extend(['position', 'inv_position', 'log_position'])
        
        # Category matching
        features.append(
            1 if impression.get('user_interest') == impression.get('ad_category') else 0
        )
        names.append('category_match')
        
        return features, names
    
    def fit(
        self,
        impressions: List[dict],
        clicks: List[int]
    ) -> 'CTRGradientBoost':
        """
        Train CTR prediction model.
        
        Args:
            impressions: List of impression data
            clicks: 1 if clicked, 0 otherwise
        """
        # Extract features
        X = []
        for impression in impressions:
            features, names = self._extract_features(impression)
            X.append(features)
        
        self.feature_names = names
        
        # Train gradient boosting
        self.gb = GradientBoostingClassifier(
            n_estimators=self.n_estimators,
            learning_rate=self.learning_rate,
            max_depth=self.max_depth,
            random_state=42
        )
        self.gb.fit(X, clicks)
        
        # Calibrate predictions
        self._calibrate(X, clicks)
        
        # Store metadata
        self.training_samples = len(impressions)
        self.training_time = datetime.now()
        self.positive_rate = sum(clicks) / len(clicks)
        
        return self
    
    def _calibrate(
        self,
        X: List[List[float]],
        y: List[int]
    ):
        """
        Calibrate predictions using Platt scaling.
        """
        # Get raw predictions
        probas = self.gb.predict_proba(X)
        raw_preds = [p[1] for p in probas]
        
        # Simple isotonic-like calibration
        # In production, use proper Platt scaling or isotonic regression
        
        # Bin predictions and compute actual rates
        n_bins = 10
        bins = [[] for _ in range(n_bins)]
        
        for pred, actual in zip(raw_preds, y):
            bin_idx = min(int(pred * n_bins), n_bins - 1)
            bins[bin_idx].append(actual)
        
        # Simple linear calibration
        pred_rates = []
        actual_rates = []
        
        for i, bin_data in enumerate(bins):
            if bin_data:
                pred_rates.append((i + 0.5) / n_bins)
                actual_rates.append(sum(bin_data) / len(bin_data))
        
        if len(pred_rates) >= 2:
            # Linear regression for calibration
            n = len(pred_rates)
            sum_x = sum(pred_rates)
            sum_y = sum(actual_rates)
            sum_xy = sum(p * a for p, a in zip(pred_rates, actual_rates))
            sum_xx = sum(p * p for p in pred_rates)
            
            denom = n * sum_xx - sum_x * sum_x
            if abs(denom) > 1e-10:
                self.calibration_a = (n * sum_xy - sum_x * sum_y) / denom
                self.calibration_b = (sum_y - self.calibration_a * sum_x) / n
    
    def predict_proba(
        self,
        impressions: List[dict]
    ) -> List[float]:
        """
        Predict click probability.
        
        Returns:
            Click probability for each impression.
        """
        X = [self._extract_features(imp)[0] for imp in impressions]
        
        # Get raw predictions
        probas = self.gb.predict_proba(X)
        raw_preds = [p[1] for p in probas]
        
        # Apply calibration
        calibrated = [
            max(0, min(1, self.calibration_a * p + self.calibration_b))
            for p in raw_preds
        ]
        
        return calibrated
    
    def rank_ads(
        self,
        user_context: dict,
        ads: List[dict]
    ) -> List[Tuple[int, float, float]]:
        """
        Rank ads by expected revenue.
        
        Returns:
            List of (ad_index, pCTR, expected_revenue) sorted by revenue.
        """
        # Create impression data for each ad
        impressions = []
        for i, ad in enumerate(ads):
            impression = {**user_context, **ad, 'position': i + 1}
            impressions.append(impression)
        
        # Predict CTR
        ctrs = self.predict_proba(impressions)
        
        # Calculate expected revenue (pCTR × bid)
        results = []
        for i, (ad, ctr) in enumerate(zip(ads, ctrs)):
            bid = ad.get('bid', 0)
            expected_revenue = ctr * bid
            results.append((i, ctr, expected_revenue))
        
        # Sort by expected revenue (descending)
        results.sort(key=lambda x: x[2], reverse=True)
        
        return results
    
    def evaluate(
        self,
        impressions: List[dict],
        clicks: List[int]
    ) -> Dict[str, float]:
        """
        Evaluate CTR model.
        """
        predictions = self.predict_proba(impressions)
        
        # Log loss
        eps = 1e-15
        log_loss = -sum(
            clicks[i] * math.log(max(predictions[i], eps)) +
            (1 - clicks[i]) * math.log(max(1 - predictions[i], eps))
            for i in range(len(clicks))
        ) / len(clicks)
        
        # AUC
        auc = self._compute_auc(clicks, predictions)
        
        # Calibration
        predicted_ctr = sum(predictions) / len(predictions)
        actual_ctr = sum(clicks) / len(clicks)
        calibration_ratio = predicted_ctr / actual_ctr if actual_ctr > 0 else 0
        
        # Accuracy at threshold 0.5
        binary_preds = [1 if p >= 0.5 else 0 for p in predictions]
        accuracy = sum(1 for p, a in zip(binary_preds, clicks) if p == a) / len(clicks)
        
        return {
            'log_loss': log_loss,
            'auc_roc': auc,
            'predicted_ctr': predicted_ctr,
            'actual_ctr': actual_ctr,
            'calibration_ratio': calibration_ratio,
            'accuracy': accuracy
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
        
        for _, label in pairs:
            if label == 1:
                tp += 1
            else:
                auc += tp
        
        return auc / (n_pos * n_neg)
    
    def get_feature_importance(self) -> List[Tuple[str, float]]:
        """Get feature importance (placeholder - needs tree access)."""
        # In a full implementation, aggregate feature importance from trees
        return list(zip(self.feature_names, [1.0 / len(self.feature_names)] * len(self.feature_names)))


# Demo
if __name__ == "__main__":
    print("Boosting Algorithms Demo")
    print("=" * 50)
    
    # AdaBoost example
    print("\n1. AdaBoost Classification:")
    random.seed(42)
    
    # Generate data
    X_train = []
    y_train = []
    for _ in range(100):
        x = [random.gauss(0, 1), random.gauss(0, 1)]
        y = 1 if x[0] + x[1] + random.gauss(0, 0.5) > 0 else -1
        X_train.append(x)
        y_train.append(y)
    
    ada = AdaBoostClassifier(n_estimators=50, learning_rate=1.0, random_state=42)
    ada.fit(X_train, y_train)
    
    print(f"   Training accuracy: {ada.score(X_train, y_train):.2%}")
    print(f"   Number of estimators: {len(ada.estimators)}")
    
    # Gradient Boosting example
    print("\n2. Gradient Boosting Regression:")
    
    X_reg = [[i] for i in range(20)]
    y_reg = [2 * i + random.gauss(0, 1) for i in range(20)]
    
    gb = GradientBoostingRegressor(
        n_estimators=50,
        learning_rate=0.1,
        max_depth=3
    )
    gb.fit(X_reg, y_reg)
    
    print(f"   R² score: {gb.score(X_reg, y_reg):.4f}")
    
    # Predictions
    test_X = [[5.5], [10.5], [15.5]]
    preds = gb.predict(test_X)
    print(f"   Predictions for {test_X}: {[f'{p:.2f}' for p in preds]}")
    
    # CTR prediction example
    print("\n3. CTR Prediction Example:")
    print("   CTR model initialized")
    print("   (Would need real impression data for training)")
```

## 9. Comparison

| Aspect | AdaBoost | Gradient Boosting | XGBoost |
|--------|----------|-------------------|---------|
| Speed | Fast | Medium | Fast |
| Accuracy | Good | Very Good | Excellent |
| Regularization | Implicit | Optional | Built-in |
| Parallelization | Limited | No | Yes |
| Handling missing | No | Manual | Automatic |
| Memory | Low | Medium | Optimized |

## 10. Best Practices

1. **Start with low learning rate** (0.01-0.1) and more trees
2. **Use early stopping** to prevent overfitting
3. **Tune max_depth** (3-8 typically sufficient)
4. **Use subsample < 1.0** for stochastic gradient boosting
5. **Monitor validation loss** during training
6. **Handle class imbalance** with sample weights

## 11. References

- [Wikipedia: Boosting](https://en.wikipedia.org/wiki/Boosting_(machine_learning))
- Freund, Y. & Schapire, R. "A Decision-Theoretic Generalization of On-Line Learning" (1997)
- Friedman, J. "Greedy Function Approximation: A Gradient Boosting Machine" (2001)
- Chen, T. & Guestrin, C. "XGBoost: A Scalable Tree Boosting System" (2016)
