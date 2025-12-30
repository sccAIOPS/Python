# Support Vector Machine (SVM)

## Overview
- **Category**: Supervised Learning / Classification & Regression
- **Complexity**: Training: O(n²d) to O(n³) | Prediction: O(sv×d)
- **Type**: Maximum margin classifier
- **Source Files**: [machine_learning/support_vector_machine.py](../../../machine_learning/support_vector_machine.py)

## 1. Mathematical Foundation

### 1.1 Linear SVM (Hard Margin)

Find hyperplane $\mathbf{w}^T\mathbf{x} + b = 0$ that separates classes with maximum margin.

**Decision function**:
$$
f(\mathbf{x}) = \text{sign}(\mathbf{w}^T\mathbf{x} + b)
$$

**Margin**: Distance between hyperplane and nearest points
$$
\text{margin} = \frac{2}{\|\mathbf{w}\|}
$$

### 1.2 Hard Margin Optimization

$$
\min_{\mathbf{w}, b} \frac{1}{2}\|\mathbf{w}\|^2
$$
$$
\text{subject to } y_i(\mathbf{w}^T\mathbf{x}_i + b) \geq 1 \quad \forall i
$$

### 1.3 Soft Margin SVM (C-SVM)

Allow misclassifications with slack variables $\xi_i$:

$$
\min_{\mathbf{w}, b, \xi} \frac{1}{2}\|\mathbf{w}\|^2 + C\sum_{i=1}^{n}\xi_i
$$
$$
\text{subject to } y_i(\mathbf{w}^T\mathbf{x}_i + b) \geq 1 - \xi_i
$$
$$
\xi_i \geq 0 \quad \forall i
$$

$C$ = regularization parameter (trade-off: margin width vs errors)

### 1.4 Lagrangian Dual Formulation

$$
\max_{\alpha} \sum_{i=1}^{n}\alpha_i - \frac{1}{2}\sum_{i=1}^{n}\sum_{j=1}^{n}\alpha_i\alpha_j y_i y_j \mathbf{x}_i^T\mathbf{x}_j
$$
$$
\text{subject to } 0 \leq \alpha_i \leq C, \quad \sum_{i=1}^{n}\alpha_i y_i = 0
$$

**Solution**:
$$
\mathbf{w} = \sum_{i=1}^{n}\alpha_i y_i \mathbf{x}_i
$$

Support vectors: samples with $\alpha_i > 0$

### 1.5 The Kernel Trick

Replace inner products with kernel function:
$$
K(\mathbf{x}_i, \mathbf{x}_j) = \phi(\mathbf{x}_i)^T\phi(\mathbf{x}_j)
$$

**Common Kernels**:

| Kernel | Formula | Use Case |
|--------|---------|----------|
| Linear | $K(\mathbf{x}, \mathbf{y}) = \mathbf{x}^T\mathbf{y}$ | Linearly separable |
| Polynomial | $K(\mathbf{x}, \mathbf{y}) = (\gamma\mathbf{x}^T\mathbf{y} + r)^d$ | Polynomial boundaries |
| RBF/Gaussian | $K(\mathbf{x}, \mathbf{y}) = \exp(-\gamma\|\mathbf{x}-\mathbf{y}\|^2)$ | Non-linear, default |
| Sigmoid | $K(\mathbf{x}, \mathbf{y}) = \tanh(\gamma\mathbf{x}^T\mathbf{y} + r)$ | Neural network-like |

### 1.6 RBF Kernel Deep Dive

$$
K(\mathbf{x}, \mathbf{y}) = \exp\left(-\frac{\|\mathbf{x}-\mathbf{y}\|^2}{2\sigma^2}\right) = \exp(-\gamma\|\mathbf{x}-\mathbf{y}\|^2)
$$

where $\gamma = \frac{1}{2\sigma^2}$

- High $\gamma$: Complex boundary (overfitting risk)
- Low $\gamma$: Simple boundary (underfitting risk)

### 1.7 Hinge Loss Perspective

SVM minimizes hinge loss:
$$
L(\mathbf{w}, b) = \frac{1}{2}\|\mathbf{w}\|^2 + C\sum_{i=1}^{n}\max(0, 1 - y_i(\mathbf{w}^T\mathbf{x}_i + b))
$$

## 2. SVM Regression (SVR)

### 2.1 ε-SVR Formulation

$$
\min_{\mathbf{w}, b, \xi, \xi^*} \frac{1}{2}\|\mathbf{w}\|^2 + C\sum_{i=1}^{n}(\xi_i + \xi_i^*)
$$

Subject to:
$$
y_i - \mathbf{w}^T\mathbf{x}_i - b \leq \epsilon + \xi_i
$$
$$
\mathbf{w}^T\mathbf{x}_i + b - y_i \leq \epsilon + \xi_i^*
$$

The $\epsilon$-insensitive tube ignores errors within $\pm\epsilon$.

## 3. Pseudocode

```
ALGORITHM SVM_Train(X, y, C, kernel)
    INPUT: Data X, labels y, regularization C, kernel K
    OUTPUT: Support vectors, alphas, bias
    
    n ← |X|
    
    // Compute kernel matrix
    K_matrix ← zeros(n, n)
    for i ← 1 to n do
        for j ← 1 to n do
            K_matrix[i,j] ← kernel(X[i], X[j])
    
    // Solve dual QP problem (using SMO or QP solver)
    α ← SolveDualQP(K_matrix, y, C)
    
    // Find support vectors
    support_vectors ← []
    support_alphas ← []
    support_labels ← []
    
    for i ← 1 to n do
        if α[i] > tolerance then
            support_vectors.append(X[i])
            support_alphas.append(α[i])
            support_labels.append(y[i])
    
    // Compute bias using support vectors on margin
    b ← 0
    count ← 0
    for i in margin_support_vectors do
        b ← b + y[i] - Σ(α[j] × y[j] × K(X[j], X[i]))
        count ← count + 1
    b ← b / count
    
    return support_vectors, support_alphas, support_labels, b

ALGORITHM SVM_Predict(x, support_vectors, alphas, labels, b, kernel)
    INPUT: Query point x, model parameters
    OUTPUT: Predicted class (-1 or +1)
    
    decision_value ← 0
    for i ← 1 to |support_vectors| do
        decision_value ← decision_value + alphas[i] × labels[i] × kernel(support_vectors[i], x)
    
    decision_value ← decision_value + b
    
    return sign(decision_value)

ALGORITHM SMO(X, y, C, kernel, max_iter, tol)
    // Sequential Minimal Optimization
    INPUT: Training data, hyperparameters
    OUTPUT: Lagrange multipliers α
    
    n ← |X|
    α ← zeros(n)
    b ← 0
    
    for iter ← 1 to max_iter do
        changed ← 0
        
        for i ← 1 to n do
            E_i ← f(X[i]) - y[i]  // Error
            
            if (y[i] × E_i < -tol and α[i] < C) or
               (y[i] × E_i > tol and α[i] > 0) then
                
                // Select j ≠ i (heuristically)
                j ← SelectSecondAlpha(i, n)
                E_j ← f(X[j]) - y[j]
                
                α_i_old ← α[i]
                α_j_old ← α[j]
                
                // Compute bounds L and H
                if y[i] ≠ y[j] then
                    L ← max(0, α[j] - α[i])
                    H ← min(C, C + α[j] - α[i])
                else
                    L ← max(0, α[i] + α[j] - C)
                    H ← min(C, α[i] + α[j])
                
                if L = H then continue
                
                // Compute η
                η ← 2 × K(X[i], X[j]) - K(X[i], X[i]) - K(X[j], X[j])
                if η ≥ 0 then continue
                
                // Update α[j]
                α[j] ← α[j] - y[j] × (E_i - E_j) / η
                α[j] ← clip(α[j], L, H)
                
                // Update α[i]
                α[i] ← α[i] + y[i] × y[j] × (α_j_old - α[j])
                
                // Update threshold b
                b ← UpdateThreshold(...)
                
                changed ← changed + 1
        
        if changed = 0 then break
    
    return α
```

## 4. Step-by-Step Example

### Example: 2D Classification

**Data**:
| Point | x₁ | x₂ | Label |
|-------|-----|-----|-------|
| A | 1 | 2 | +1 |
| B | 2 | 3 | +1 |
| C | 3 | 3 | +1 |
| D | 6 | 5 | -1 |
| E | 7 | 4 | -1 |
| F | 8 | 6 | -1 |

**Step 1**: Compute kernel matrix (linear)
```
K[i,j] = x_i · x_j
```

**Step 2**: Solve dual QP
- α = [0, 0.625, 0, 0.625, 0, 0] (example)
- Support vectors: B(2,3), D(6,5)

**Step 3**: Compute w and b
$$
\mathbf{w} = 0.625 \times (+1) \times [2,3] + 0.625 \times (-1) \times [6,5] = [-2.5, -1.25]
$$

$$
b = 1 - \mathbf{w}^T\mathbf{x}_B = 1 - (-2.5×2 - 1.25×3) = 9.75
$$

**Decision boundary**: $-2.5x_1 - 1.25x_2 + 9.75 = 0$

## 5. Complexity Analysis

| Operation | Time Complexity | Space Complexity |
|-----------|-----------------|------------------|
| Training (SMO) | O(n²) to O(n³) | O(n²) |
| Training (QP) | O(n³) | O(n²) |
| Prediction | O(n_sv × d) | O(n_sv) |
| Kernel evaluation | O(d) | O(1) |

Where n = samples, d = features, n_sv = support vectors.

## 6. Visual Representation

### 6.1 Maximum Margin

```
        │     /
    +   │    /  ← Decision boundary
   + +  │   /
        │  /    Margin
  ──────┼─/─────────────
        │/
       /│     - -
      / │       -
     /  │
    /   │     Margin = 2/||w||
```

### 6.2 Soft Margin

```
     +    │    /
    + +   │   / ←ξ   (slack)
      × + │  /   
    ──────┼─/─────
          │/
         /│    -
        / │  × -  (misclassified)
```

### 6.3 Kernel Transformation

```
Input Space:              Feature Space:
                          
   ○ ○ ○ ○                    ○ ○ ○ ○
   ● ● ● ●    ───────►       ─────────
   ○ ○ ○ ○    φ(x)            ● ● ● ●
                              ○ ○ ○ ○
Not separable              Linearly separable
```

## 7. Implementation

```python
from typing import List, Tuple, Callable, Optional
import math
import random


def linear_kernel(x: List[float], y: List[float]) -> float:
    """
    Linear kernel: K(x,y) = x·y
    
    >>> linear_kernel([1, 2], [3, 4])
    11
    """
    return sum(xi * yi for xi, yi in zip(x, y))


def polynomial_kernel(
    x: List[float],
    y: List[float],
    degree: int = 3,
    gamma: float = 1.0,
    coef0: float = 0.0
) -> float:
    """
    Polynomial kernel: K(x,y) = (γ⋅x·y + r)^d
    
    >>> polynomial_kernel([1, 2], [3, 4], degree=2)
    121.0
    """
    return (gamma * linear_kernel(x, y) + coef0) ** degree


def rbf_kernel(
    x: List[float],
    y: List[float],
    gamma: float = 1.0
) -> float:
    """
    RBF (Gaussian) kernel: K(x,y) = exp(-γ||x-y||²)
    
    >>> abs(rbf_kernel([0, 0], [0, 0]) - 1.0) < 0.001
    True
    >>> rbf_kernel([0, 0], [1, 1]) < 1.0
    True
    """
    sq_dist = sum((xi - yi) ** 2 for xi, yi in zip(x, y))
    return math.exp(-gamma * sq_dist)


def sigmoid_kernel(
    x: List[float],
    y: List[float],
    gamma: float = 0.01,
    coef0: float = 0.0
) -> float:
    """Sigmoid kernel: K(x,y) = tanh(γ⋅x·y + r)"""
    return math.tanh(gamma * linear_kernel(x, y) + coef0)


class SVM:
    """
    Support Vector Machine for binary classification.
    
    Uses SMO (Sequential Minimal Optimization) algorithm.
    """
    
    def __init__(
        self,
        kernel: str = 'rbf',
        C: float = 1.0,
        gamma: float = 0.1,
        degree: int = 3,
        coef0: float = 0.0,
        tol: float = 1e-3,
        max_iter: int = 1000
    ):
        """
        Initialize SVM.
        
        Args:
            kernel: 'linear', 'poly', 'rbf', or 'sigmoid'
            C: Regularization parameter
            gamma: Kernel coefficient
            degree: Polynomial degree
            coef0: Independent term in poly/sigmoid
            tol: Tolerance for stopping
            max_iter: Maximum iterations
        """
        self.kernel = kernel
        self.C = C
        self.gamma = gamma
        self.degree = degree
        self.coef0 = coef0
        self.tol = tol
        self.max_iter = max_iter
        
        self.alphas: Optional[List[float]] = None
        self.b: float = 0.0
        self.X: Optional[List[List[float]]] = None
        self.y: Optional[List[int]] = None
        self.support_vectors_indices: List[int] = []
    
    def _kernel_func(self, x: List[float], y: List[float]) -> float:
        """Compute kernel between two points."""
        if self.kernel == 'linear':
            return linear_kernel(x, y)
        elif self.kernel == 'poly':
            return polynomial_kernel(x, y, self.degree, self.gamma, self.coef0)
        elif self.kernel == 'rbf':
            return rbf_kernel(x, y, self.gamma)
        elif self.kernel == 'sigmoid':
            return sigmoid_kernel(x, y, self.gamma, self.coef0)
        else:
            raise ValueError(f"Unknown kernel: {self.kernel}")
    
    def _decision_function(self, x: List[float]) -> float:
        """Compute decision function value."""
        result = 0.0
        for i in self.support_vectors_indices:
            result += self.alphas[i] * self.y[i] * self._kernel_func(self.X[i], x)
        return result + self.b
    
    def fit(
        self,
        X: List[List[float]],
        y: List[int]
    ) -> 'SVM':
        """
        Train SVM using SMO algorithm.
        
        Args:
            X: Training features
            y: Training labels (-1 or +1)
        
        >>> svm = SVM(kernel='linear', C=1.0)
        >>> X = [[0, 0], [1, 1], [2, 0], [2, 1]]
        >>> y = [1, 1, -1, -1]
        >>> svm.fit(X, y) is not None
        True
        """
        self.X = X
        self.y = y
        n = len(X)
        
        # Initialize alphas
        self.alphas = [0.0] * n
        self.b = 0.0
        
        # Precompute kernel matrix
        K = [[self._kernel_func(X[i], X[j]) for j in range(n)] for i in range(n)]
        
        # SMO algorithm
        passes = 0
        while passes < self.max_iter:
            num_changed = 0
            
            for i in range(n):
                Ei = self._compute_error(i, K)
                
                if ((y[i] * Ei < -self.tol and self.alphas[i] < self.C) or
                    (y[i] * Ei > self.tol and self.alphas[i] > 0)):
                    
                    # Select j randomly
                    j = i
                    while j == i:
                        j = random.randint(0, n - 1)
                    
                    Ej = self._compute_error(j, K)
                    
                    alpha_i_old = self.alphas[i]
                    alpha_j_old = self.alphas[j]
                    
                    # Compute bounds
                    if y[i] != y[j]:
                        L = max(0, self.alphas[j] - self.alphas[i])
                        H = min(self.C, self.C + self.alphas[j] - self.alphas[i])
                    else:
                        L = max(0, self.alphas[i] + self.alphas[j] - self.C)
                        H = min(self.C, self.alphas[i] + self.alphas[j])
                    
                    if L == H:
                        continue
                    
                    # Compute eta
                    eta = 2 * K[i][j] - K[i][i] - K[j][j]
                    if eta >= 0:
                        continue
                    
                    # Update alpha_j
                    self.alphas[j] = alpha_j_old - y[j] * (Ei - Ej) / eta
                    self.alphas[j] = min(H, max(L, self.alphas[j]))
                    
                    if abs(self.alphas[j] - alpha_j_old) < 1e-5:
                        continue
                    
                    # Update alpha_i
                    self.alphas[i] = alpha_i_old + y[i] * y[j] * (alpha_j_old - self.alphas[j])
                    
                    # Update threshold
                    b1 = self.b - Ei - y[i] * (self.alphas[i] - alpha_i_old) * K[i][i] - \
                         y[j] * (self.alphas[j] - alpha_j_old) * K[i][j]
                    b2 = self.b - Ej - y[i] * (self.alphas[i] - alpha_i_old) * K[i][j] - \
                         y[j] * (self.alphas[j] - alpha_j_old) * K[j][j]
                    
                    if 0 < self.alphas[i] < self.C:
                        self.b = b1
                    elif 0 < self.alphas[j] < self.C:
                        self.b = b2
                    else:
                        self.b = (b1 + b2) / 2
                    
                    num_changed += 1
            
            if num_changed == 0:
                passes += 1
            else:
                passes = 0
        
        # Store support vector indices
        self.support_vectors_indices = [
            i for i in range(n) if self.alphas[i] > 1e-5
        ]
        
        return self
    
    def _compute_error(self, i: int, K: List[List[float]]) -> float:
        """Compute prediction error for sample i."""
        result = 0.0
        for j in range(len(self.X)):
            result += self.alphas[j] * self.y[j] * K[i][j]
        return result + self.b - self.y[i]
    
    def predict(self, X: List[List[float]]) -> List[int]:
        """
        Predict class labels.
        
        >>> svm = SVM(kernel='linear')
        >>> svm.fit([[0, 0], [1, 1], [3, 3], [4, 4]], [1, 1, -1, -1])
        <...>
        >>> preds = svm.predict([[0.5, 0.5], [3.5, 3.5]])
        >>> preds[0] == 1 and preds[1] == -1
        True
        """
        return [1 if self._decision_function(x) >= 0 else -1 for x in X]
    
    def decision_function(self, X: List[List[float]]) -> List[float]:
        """Return decision function values."""
        return [self._decision_function(x) for x in X]
    
    def score(
        self,
        X: List[List[float]],
        y: List[int]
    ) -> float:
        """
        Compute accuracy.
        
        >>> svm = SVM(kernel='linear')
        >>> svm.fit([[0, 0], [1, 1], [3, 3], [4, 4]], [1, 1, -1, -1])
        <...>
        >>> svm.score([[0, 0], [4, 4]], [1, -1])
        1.0
        """
        predictions = self.predict(X)
        correct = sum(1 for p, yi in zip(predictions, y) if p == yi)
        return correct / len(y)
    
    @property
    def support_vectors(self) -> List[List[float]]:
        """Return support vectors."""
        return [self.X[i] for i in self.support_vectors_indices]
    
    @property
    def n_support(self) -> int:
        """Return number of support vectors."""
        return len(self.support_vectors_indices)


class MulticlassSVM:
    """
    Multiclass SVM using one-vs-rest strategy.
    """
    
    def __init__(self, **kwargs):
        """Initialize with SVM parameters."""
        self.kwargs = kwargs
        self.classifiers: dict = {}
        self.classes: List[int] = []
    
    def fit(
        self,
        X: List[List[float]],
        y: List[int]
    ) -> 'MulticlassSVM':
        """
        Train one SVM per class.
        
        >>> msvm = MulticlassSVM(kernel='linear', C=1.0)
        >>> X = [[0,0], [1,0], [0,1], [1,1], [5,5], [6,5], [5,6], [6,6]]
        >>> y = [0, 0, 0, 0, 1, 1, 1, 1]
        >>> msvm.fit(X, y) is not None
        True
        """
        self.classes = list(set(y))
        
        for cls in self.classes:
            # One-vs-rest: current class = +1, others = -1
            y_binary = [1 if yi == cls else -1 for yi in y]
            
            svm = SVM(**self.kwargs)
            svm.fit(X, y_binary)
            self.classifiers[cls] = svm
        
        return self
    
    def predict(self, X: List[List[float]]) -> List[int]:
        """
        Predict class with highest decision value.
        """
        predictions = []
        
        for x in X:
            scores = {
                cls: svm._decision_function(x)
                for cls, svm in self.classifiers.items()
            }
            predictions.append(max(scores, key=scores.get))
        
        return predictions


class SVR:
    """
    Support Vector Regression.
    """
    
    def __init__(
        self,
        kernel: str = 'rbf',
        C: float = 1.0,
        epsilon: float = 0.1,
        gamma: float = 0.1,
        max_iter: int = 1000,
        tol: float = 1e-3
    ):
        """
        Initialize SVR.
        
        Args:
            kernel: Kernel type
            C: Regularization
            epsilon: Epsilon-tube width
            gamma: Kernel parameter
        """
        self.kernel = kernel
        self.C = C
        self.epsilon = epsilon
        self.gamma = gamma
        self.max_iter = max_iter
        self.tol = tol
        
        self.alphas: Optional[List[float]] = None
        self.alphas_star: Optional[List[float]] = None
        self.b: float = 0.0
        self.X: Optional[List[List[float]]] = None
        self.y: Optional[List[float]] = None
    
    def _kernel_func(self, x: List[float], y: List[float]) -> float:
        """Compute kernel."""
        if self.kernel == 'linear':
            return linear_kernel(x, y)
        elif self.kernel == 'rbf':
            return rbf_kernel(x, y, self.gamma)
        else:
            raise ValueError(f"Unknown kernel: {self.kernel}")
    
    def fit(
        self,
        X: List[List[float]],
        y: List[float]
    ) -> 'SVR':
        """
        Train SVR (simplified SMO).
        """
        self.X = X
        self.y = y
        n = len(X)
        
        # Initialize
        self.alphas = [0.0] * n
        self.alphas_star = [0.0] * n
        self.b = 0.0
        
        # Simplified training (gradient descent approach)
        for _ in range(self.max_iter):
            for i in range(n):
                prediction = self._predict_single(X[i])
                error = prediction - y[i]
                
                if abs(error) > self.epsilon:
                    # Update alphas
                    if error > self.epsilon:
                        delta = min(self.C - self.alphas_star[i], 
                                  (error - self.epsilon) / self._kernel_func(X[i], X[i]))
                        self.alphas_star[i] += delta
                    else:
                        delta = min(self.C - self.alphas[i],
                                  (-error - self.epsilon) / self._kernel_func(X[i], X[i]))
                        self.alphas[i] += delta
        
        # Compute bias
        count = 0
        for i in range(n):
            if 0 < self.alphas[i] < self.C or 0 < self.alphas_star[i] < self.C:
                self.b += y[i] - self._predict_single(X[i])
                count += 1
        
        if count > 0:
            self.b /= count
        
        return self
    
    def _predict_single(self, x: List[float]) -> float:
        """Predict single sample."""
        result = 0.0
        for i in range(len(self.X)):
            result += (self.alphas[i] - self.alphas_star[i]) * self._kernel_func(self.X[i], x)
        return result + self.b
    
    def predict(self, X: List[List[float]]) -> List[float]:
        """Predict values."""
        return [self._predict_single(x) for x in X]
```

## 8. Applications

### 8.1 Common Use Cases

- Image classification
- Text categorization
- Bioinformatics (gene classification)
- Handwriting recognition
- Face detection
- Spam filtering

### 8.2 When to Use SVM

- Medium-sized datasets (n < 100K)
- High-dimensional data
- Clear margin of separation
- Need probabilistic outputs (with Platt scaling)

## 9. Real-World Software Engineering Applications

### 9.1 Production Example: Fraud Detection System

```python
from typing import List, Tuple, Dict, Optional
import math
import random
from datetime import datetime


class FraudDetectionSVM:
    """
    Production SVM-based fraud detection system.
    """
    
    def __init__(
        self,
        kernel: str = 'rbf',
        C: float = 10.0,
        gamma: float = 0.01,
        class_weight: Optional[Dict[int, float]] = None
    ):
        """
        Initialize fraud detector.
        
        Args:
            kernel: Kernel type
            C: Regularization (higher for fraud)
            gamma: RBF parameter
            class_weight: Handle class imbalance
        """
        self.svm = SVM(kernel=kernel, C=C, gamma=gamma)
        self.class_weight = class_weight or {1: 1.0, -1: 1.0}
        
        # Feature scaling parameters
        self.mean: Optional[List[float]] = None
        self.std: Optional[List[float]] = None
        
        # Model info
        self.threshold: float = 0.0
        self.feature_names: List[str] = []
    
    def extract_features(
        self,
        transaction: dict
    ) -> List[float]:
        """
        Extract features from transaction.
        
        >>> fd = FraudDetectionSVM()
        >>> txn = {'amount': 100, 'hour': 14, 'merchant_category': 'retail'}
        >>> features = fd.extract_features(txn)
        >>> len(features) > 0
        True
        """
        features = []
        
        # Amount features
        amount = transaction.get('amount', 0)
        features.append(amount)
        features.append(math.log1p(amount))
        
        # Time features
        hour = transaction.get('hour', 12)
        features.append(hour)
        features.append(1 if 0 <= hour <= 6 else 0)  # Night transaction
        
        # Location features
        features.append(transaction.get('distance_from_home', 0))
        features.append(transaction.get('distance_from_last', 0))
        
        # Velocity features
        features.append(transaction.get('transactions_last_hour', 0))
        features.append(transaction.get('amount_last_hour', 0))
        
        # Merchant features
        category = transaction.get('merchant_category', 'other')
        high_risk_categories = ['gambling', 'crypto', 'wire_transfer']
        features.append(1 if category in high_risk_categories else 0)
        
        # Card features
        features.append(transaction.get('card_present', 1))
        features.append(transaction.get('international', 0))
        
        return features
    
    def _scale_features(
        self,
        X: List[List[float]],
        fit: bool = False
    ) -> List[List[float]]:
        """Standardize features."""
        if fit:
            n_features = len(X[0])
            self.mean = [0.0] * n_features
            self.std = [1.0] * n_features
            
            for j in range(n_features):
                values = [X[i][j] for i in range(len(X))]
                self.mean[j] = sum(values) / len(values)
                variance = sum((v - self.mean[j]) ** 2 for v in values) / len(values)
                self.std[j] = math.sqrt(variance) if variance > 0 else 1.0
        
        scaled = []
        for x in X:
            scaled.append([
                (x[j] - self.mean[j]) / self.std[j]
                for j in range(len(x))
            ])
        
        return scaled
    
    def fit(
        self,
        transactions: List[dict],
        labels: List[int]
    ) -> 'FraudDetectionSVM':
        """
        Train fraud detection model.
        
        Args:
            transactions: Transaction dictionaries
            labels: 1 for fraud, -1 for legitimate
        """
        # Extract features
        X = [self.extract_features(t) for t in transactions]
        
        # Scale features
        X_scaled = self._scale_features(X, fit=True)
        
        # Handle class imbalance by oversampling minority
        X_balanced, y_balanced = self._balance_classes(X_scaled, labels)
        
        # Train SVM
        self.svm.fit(X_balanced, y_balanced)
        
        # Find optimal threshold using validation
        self._optimize_threshold(X_scaled, labels)
        
        return self
    
    def _balance_classes(
        self,
        X: List[List[float]],
        y: List[int]
    ) -> Tuple[List[List[float]], List[int]]:
        """Oversample minority class."""
        pos_indices = [i for i, yi in enumerate(y) if yi == 1]
        neg_indices = [i for i, yi in enumerate(y) if yi == -1]
        
        # Oversample positive class (fraud)
        ratio = len(neg_indices) // max(len(pos_indices), 1)
        
        X_new = list(X)
        y_new = list(y)
        
        for _ in range(min(ratio - 1, 10)):  # Limit oversampling
            for idx in pos_indices:
                # Add with small noise
                noisy = [
                    x + random.gauss(0, 0.01) for x in X[idx]
                ]
                X_new.append(noisy)
                y_new.append(1)
        
        return X_new, y_new
    
    def _optimize_threshold(
        self,
        X: List[List[float]],
        y: List[int]
    ):
        """Find threshold that maximizes F1 score."""
        scores = self.svm.decision_function(X)
        
        best_f1 = 0.0
        best_threshold = 0.0
        
        for threshold in [s for s in sorted(set(scores))]:
            predictions = [1 if s >= threshold else -1 for s in scores]
            
            tp = sum(1 for p, yi in zip(predictions, y) if p == 1 and yi == 1)
            fp = sum(1 for p, yi in zip(predictions, y) if p == 1 and yi == -1)
            fn = sum(1 for p, yi in zip(predictions, y) if p == -1 and yi == 1)
            
            precision = tp / (tp + fp) if tp + fp > 0 else 0
            recall = tp / (tp + fn) if tp + fn > 0 else 0
            f1 = 2 * precision * recall / (precision + recall) if precision + recall > 0 else 0
            
            if f1 > best_f1:
                best_f1 = f1
                best_threshold = threshold
        
        self.threshold = best_threshold
    
    def predict(
        self,
        transactions: List[dict]
    ) -> List[int]:
        """
        Predict fraud labels.
        
        Returns:
            1 for fraud, -1 for legitimate.
        """
        X = [self.extract_features(t) for t in transactions]
        X_scaled = self._scale_features(X, fit=False)
        
        scores = self.svm.decision_function(X_scaled)
        return [1 if s >= self.threshold else -1 for s in scores]
    
    def predict_proba(
        self,
        transactions: List[dict]
    ) -> List[Tuple[float, float]]:
        """
        Return fraud probability estimates (Platt scaling).
        """
        X = [self.extract_features(t) for t in transactions]
        X_scaled = self._scale_features(X, fit=False)
        
        scores = self.svm.decision_function(X_scaled)
        
        # Sigmoid transformation (Platt scaling approximation)
        probas = []
        for s in scores:
            p_fraud = 1 / (1 + math.exp(-s))
            probas.append((1 - p_fraud, p_fraud))
        
        return probas
    
    def evaluate(
        self,
        transactions: List[dict],
        labels: List[int]
    ) -> Dict[str, float]:
        """
        Comprehensive evaluation metrics.
        """
        predictions = self.predict(transactions)
        
        tp = sum(1 for p, y in zip(predictions, labels) if p == 1 and y == 1)
        tn = sum(1 for p, y in zip(predictions, labels) if p == -1 and y == -1)
        fp = sum(1 for p, y in zip(predictions, labels) if p == 1 and y == -1)
        fn = sum(1 for p, y in zip(predictions, labels) if p == -1 and y == 1)
        
        accuracy = (tp + tn) / len(labels) if labels else 0
        precision = tp / (tp + fp) if tp + fp > 0 else 0
        recall = tp / (tp + fn) if tp + fn > 0 else 0
        f1 = 2 * precision * recall / (precision + recall) if precision + recall > 0 else 0
        
        # Fraud detection specific
        false_positive_rate = fp / (fp + tn) if fp + tn > 0 else 0
        
        return {
            'accuracy': accuracy,
            'precision': precision,
            'recall': recall,
            'f1_score': f1,
            'false_positive_rate': false_positive_rate,
            'true_positives': tp,
            'false_positives': fp,
            'true_negatives': tn,
            'false_negatives': fn
        }


class TextClassifierSVM:
    """
    SVM-based text classification (spam, sentiment, etc.).
    """
    
    def __init__(
        self,
        kernel: str = 'linear',
        C: float = 1.0,
        max_features: int = 5000
    ):
        self.svm = SVM(kernel=kernel, C=C)
        self.max_features = max_features
        
        self.vocabulary: Dict[str, int] = {}
        self.idf: Dict[str, float] = {}
    
    def _tokenize(self, text: str) -> List[str]:
        """Simple tokenization."""
        import re
        text = text.lower()
        tokens = re.findall(r'\b[a-z]+\b', text)
        return tokens
    
    def _build_vocabulary(
        self,
        documents: List[str]
    ):
        """Build vocabulary from documents."""
        word_counts: Dict[str, int] = {}
        doc_counts: Dict[str, int] = {}
        
        for doc in documents:
            tokens = set(self._tokenize(doc))
            
            for token in tokens:
                doc_counts[token] = doc_counts.get(token, 0) + 1
            
            for token in self._tokenize(doc):
                word_counts[token] = word_counts.get(token, 0) + 1
        
        # Select top features
        sorted_words = sorted(
            word_counts.items(),
            key=lambda x: x[1],
            reverse=True
        )[:self.max_features]
        
        self.vocabulary = {
            word: idx for idx, (word, _) in enumerate(sorted_words)
        }
        
        # Compute IDF
        n_docs = len(documents)
        for word, idx in self.vocabulary.items():
            df = doc_counts.get(word, 1)
            self.idf[word] = math.log(n_docs / df)
    
    def _vectorize(self, text: str) -> List[float]:
        """Convert text to TF-IDF vector."""
        tokens = self._tokenize(text)
        
        # Term frequency
        tf: Dict[str, int] = {}
        for token in tokens:
            tf[token] = tf.get(token, 0) + 1
        
        # TF-IDF vector
        vector = [0.0] * len(self.vocabulary)
        for word, idx in self.vocabulary.items():
            if word in tf:
                vector[idx] = tf[word] * self.idf.get(word, 0)
        
        # L2 normalize
        norm = math.sqrt(sum(v ** 2 for v in vector))
        if norm > 0:
            vector = [v / norm for v in vector]
        
        return vector
    
    def fit(
        self,
        documents: List[str],
        labels: List[int]
    ) -> 'TextClassifierSVM':
        """Train text classifier."""
        self._build_vocabulary(documents)
        
        X = [self._vectorize(doc) for doc in documents]
        self.svm.fit(X, labels)
        
        return self
    
    def predict(self, documents: List[str]) -> List[int]:
        """Predict document labels."""
        X = [self._vectorize(doc) for doc in documents]
        return self.svm.predict(X)


# Demo
if __name__ == "__main__":
    print("Support Vector Machine Demo")
    print("=" * 50)
    
    # Simple 2D classification
    X_train = [
        [1, 2], [2, 1], [2, 3], [3, 2],  # Class +1
        [6, 5], [7, 6], [6, 7], [7, 7]   # Class -1
    ]
    y_train = [1, 1, 1, 1, -1, -1, -1, -1]
    
    # Train SVM
    svm = SVM(kernel='rbf', C=1.0, gamma=0.5)
    svm.fit(X_train, y_train)
    
    print(f"Number of support vectors: {svm.n_support}")
    print(f"Training accuracy: {svm.score(X_train, y_train):.2%}")
    
    # Test
    X_test = [[2, 2], [6, 6], [4, 4]]
    predictions = svm.predict(X_test)
    print(f"Test predictions: {predictions}")
    
    print("\n" + "=" * 50)
    print("Fraud Detection Example")
    print("=" * 50)
    
    # Sample transactions
    transactions = [
        {'amount': 50, 'hour': 10, 'distance_from_home': 5, 'transactions_last_hour': 2},
        {'amount': 100, 'hour': 14, 'distance_from_home': 10, 'transactions_last_hour': 1},
        {'amount': 5000, 'hour': 3, 'distance_from_home': 500, 'transactions_last_hour': 5},
        {'amount': 3000, 'hour': 2, 'distance_from_home': 1000, 'transactions_last_hour': 8},
    ]
    labels = [-1, -1, 1, 1]  # -1 = legitimate, 1 = fraud
    
    fd = FraudDetectionSVM(kernel='rbf', C=10.0, gamma=0.1)
    fd.fit(transactions, labels)
    
    # Test transaction
    test_txn = {'amount': 4000, 'hour': 3, 'distance_from_home': 800, 'transactions_last_hour': 6}
    pred = fd.predict([test_txn])
    print(f"Transaction prediction: {'FRAUD' if pred[0] == 1 else 'LEGITIMATE'}")
```

## 10. Comparison with Other Classifiers

| Aspect | SVM | Logistic Regression | Random Forest |
|--------|-----|---------------------|---------------|
| Interpretability | Medium | High | Low |
| Training time | O(n²-n³) | O(nd) | O(n log n × trees) |
| Prediction time | O(n_sv × d) | O(d) | O(depth × trees) |
| High-dim data | Excellent | Good | Poor |
| Non-linear | With kernels | No | Yes |
| Class imbalance | With weights | With weights | Good |

## 11. Best Practices

1. **Always scale features** - SVM sensitive to scale
2. **Use RBF kernel** as default
3. **Grid search C and gamma**
4. **Handle imbalanced classes** with weights or SMOTE
5. **Use linear kernel** for high-dimensional sparse data
6. **Cross-validate** hyperparameters

## 12. References

- [Wikipedia: Support Vector Machine](https://en.wikipedia.org/wiki/Support_vector_machine)
- Vapnik, V. "The Nature of Statistical Learning Theory" (1995)
- Platt, J. "Sequential Minimal Optimization" (1998)
- Scholkopf, B. "Learning with Kernels" (2002)
