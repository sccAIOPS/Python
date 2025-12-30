# Perceptron

## Overview
- **Category**: Supervised Learning / Neural Networks
- **Complexity**: Time: O(nde) | Space: O(d)
- **Type**: Linear binary classifier
- **Source Files**: [machine_learning/perceptron.py](../../../machine_learning/perceptron.py), [neural_network/perceptron.py](../../../neural_network/perceptron.py)

## 1. Mathematical Foundation

### 1.1 The Perceptron Model

A perceptron is a single artificial neuron that computes:

$$
f(\mathbf{x}) = \sigma\left(\sum_{i=1}^{d} w_i x_i + b\right) = \sigma(\mathbf{w}^T\mathbf{x} + b)
$$

where:
- $\mathbf{x} = (x_1, ..., x_d)$ = input features
- $\mathbf{w} = (w_1, ..., w_d)$ = weights
- $b$ = bias
- $\sigma$ = activation function

### 1.2 Activation Functions

**Step function (original perceptron)**:
$$
\sigma(z) = \begin{cases} 1 & \text{if } z \geq 0 \\ 0 & \text{if } z < 0 \end{cases}
$$

**Sign function** (for ±1 labels):
$$
\sigma(z) = \text{sign}(z) = \begin{cases} +1 & \text{if } z \geq 0 \\ -1 & \text{if } z < 0 \end{cases}
$$

### 1.3 Perceptron Learning Rule

For misclassified sample $(\mathbf{x}_i, y_i)$:

$$
\mathbf{w}_{t+1} = \mathbf{w}_t + \eta y_i \mathbf{x}_i
$$
$$
b_{t+1} = b_t + \eta y_i
$$

where $\eta$ is the learning rate.

### 1.4 Geometric Interpretation

- Perceptron finds hyperplane $\mathbf{w}^T\mathbf{x} + b = 0$
- Update moves hyperplane toward misclassified point
- Converges if data is linearly separable

### 1.5 Perceptron Convergence Theorem

If data is linearly separable with margin $\gamma$ and $\|\mathbf{x}\| \leq R$:

$$
\text{Number of mistakes} \leq \left(\frac{R}{\gamma}\right)^2
$$

### 1.6 Loss Function View

Perceptron minimizes perceptron loss:
$$
L(\mathbf{w}, b) = \sum_{i \in \mathcal{M}} -y_i(\mathbf{w}^T\mathbf{x}_i + b)
$$

where $\mathcal{M}$ = set of misclassified samples.

## 2. Variants

### 2.1 Voted Perceptron

Store all weight vectors encountered during training:
$$
\hat{y} = \text{sign}\left(\sum_{k=1}^{K} c_k \cdot \text{sign}(\mathbf{w}_k^T\mathbf{x} + b_k)\right)
$$

where $c_k$ = survival time of weight vector $k$.

### 2.2 Averaged Perceptron

Average over all weight vectors:
$$
\bar{\mathbf{w}} = \frac{1}{T}\sum_{t=1}^{T}\mathbf{w}_t
$$

### 2.3 Kernel Perceptron

Use kernel trick for non-linear boundaries:
$$
f(\mathbf{x}) = \text{sign}\left(\sum_{i=1}^{n} \alpha_i y_i K(\mathbf{x}_i, \mathbf{x}) + b\right)
$$

### 2.4 Pocket Algorithm

Keep track of best weights seen (for non-separable data):
- Store weights with lowest error rate
- Return pocket weights at end

## 3. Pseudocode

```
ALGORITHM Perceptron(X, y, η, max_epochs)
    INPUT: Data X, labels y ∈ {-1, +1}, learning rate η, max epochs
    OUTPUT: Weights w, bias b
    
    // Initialize
    w ← zeros(d)
    b ← 0
    
    for epoch ← 1 to max_epochs do
        errors ← 0
        
        for i ← 1 to n do
            // Compute prediction
            z ← w · X[i] + b
            ŷ ← sign(z)
            
            // Update if misclassified
            if ŷ ≠ y[i] then
                w ← w + η × y[i] × X[i]
                b ← b + η × y[i]
                errors ← errors + 1
        
        // Early stopping if converged
        if errors = 0 then
            break
    
    return w, b

ALGORITHM AveragedPerceptron(X, y, η, max_epochs)
    INPUT: Data, labels, learning rate, epochs
    OUTPUT: Averaged weights
    
    w ← zeros(d)
    b ← 0
    w_sum ← zeros(d)
    b_sum ← 0
    count ← 0
    
    for epoch ← 1 to max_epochs do
        for i ← 1 to n do
            z ← w · X[i] + b
            ŷ ← sign(z)
            
            if ŷ ≠ y[i] then
                w ← w + η × y[i] × X[i]
                b ← b + η × y[i]
            
            w_sum ← w_sum + w
            b_sum ← b_sum + b
            count ← count + 1
    
    return w_sum / count, b_sum / count

ALGORITHM VotedPerceptron(X, y, η, max_epochs)
    INPUT: Data, labels, learning rate, epochs
    OUTPUT: List of (weight, bias, count) tuples
    
    w ← zeros(d)
    b ← 0
    c ← 0  // survival count
    voters ← []
    
    for epoch ← 1 to max_epochs do
        for i ← 1 to n do
            z ← w · X[i] + b
            
            if sign(z) ≠ y[i] then
                voters.append((w.copy(), b, c))
                w ← w + η × y[i] × X[i]
                b ← b + η × y[i]
                c ← 1
            else
                c ← c + 1
    
    voters.append((w, b, c))
    return voters
```

## 4. Step-by-Step Example

### Example: Learning AND Gate

**Data**:
| x₁ | x₂ | y |
|----|----|---|
| 0 | 0 | -1 |
| 0 | 1 | -1 |
| 1 | 0 | -1 |
| 1 | 1 | +1 |

**Training with η = 1**:

| Epoch | Sample | w₁ | w₂ | b | Prediction | Update? |
|-------|--------|-----|-----|---|------------|---------|
| 1 | (0,0,-1) | 0 | 0 | 0 | +1 (0≥0) | Yes |
| | | 0 | 0 | -1 | | w=w-1×(0,0), b=b-1 |
| 1 | (0,1,-1) | 0 | 0 | -1 | -1 (-1<0) | No |
| 1 | (1,0,-1) | 0 | 0 | -1 | -1 (-1<0) | No |
| 1 | (1,1,+1) | 0 | 0 | -1 | -1 (-1<0) | Yes |
| | | 1 | 1 | 0 | | w=w+1×(1,1), b=b+1 |
| 2 | (0,0,-1) | 1 | 1 | 0 | +1 (0≥0) | Yes |
| | | 1 | 1 | -1 | | b=b-1 |
| ... | ... | ... | ... | ... | ... | ... |

**Final**: w = (1, 1), b = -1.5 (approximately)

**Decision boundary**: $x_1 + x_2 - 1.5 = 0$

## 5. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Single update | O(d) | O(d) |
| One epoch | O(nd) | O(d) |
| Training | O(nde) | O(d) |
| Prediction | O(d) | O(1) |
| Voted prediction | O(Kd) | O(Kd) |

Where n = samples, d = features, e = epochs, K = voters.

## 6. Visual Representation

### 6.1 Perceptron Structure

```
        x₁ ──[w₁]──╮
                    ╲
        x₂ ──[w₂]───○──[Σ]──[σ]──→ ŷ
                    ╱
        x₃ ──[w₃]──╯
             ↑
            [b]
```

### 6.2 Decision Boundary Evolution

```
Epoch 1:        Epoch 3:        Converged:
                    
   +  +            +  +           +  +
      /           /    \            \
   - / -       - /      \        -   \ -
    /             /   -              -\
   - -          - -                 - -
   
  Misaligned    Adjusting       Separates
```

### 6.3 XOR Problem (Not Linearly Separable)

```
    (0,1) ●────────○ (1,1)
          │        │
          │   ?    │     No single line
          │        │     can separate
    (0,0) ○────────● (1,0)
    
    ● = Class 1, ○ = Class 0
```

## 7. Implementation

```python
from typing import List, Tuple, Optional, Callable
import random
import math


class Perceptron:
    """
    Standard Perceptron classifier.
    
    A linear classifier that learns a hyperplane to separate classes.
    """
    
    def __init__(
        self,
        learning_rate: float = 1.0,
        max_epochs: int = 1000,
        random_state: Optional[int] = None
    ):
        """
        Initialize Perceptron.
        
        Args:
            learning_rate: Step size for weight updates
            max_epochs: Maximum training iterations
            random_state: Seed for reproducibility
        """
        self.lr = learning_rate
        self.max_epochs = max_epochs
        self.random_state = random_state
        
        self.weights: Optional[List[float]] = None
        self.bias: float = 0.0
        self.n_updates: int = 0
    
    def _dot(self, x: List[float], y: List[float]) -> float:
        """Compute dot product."""
        return sum(xi * yi for xi, yi in zip(x, y))
    
    def _sign(self, z: float) -> int:
        """Sign activation function."""
        return 1 if z >= 0 else -1
    
    def fit(
        self,
        X: List[List[float]],
        y: List[int]
    ) -> 'Perceptron':
        """
        Train perceptron.
        
        Args:
            X: Training features
            y: Labels (-1 or +1)
        
        Returns:
            Self
        
        >>> p = Perceptron(learning_rate=1.0, max_epochs=100)
        >>> X = [[0, 0], [0, 1], [1, 0], [1, 1]]
        >>> y = [-1, -1, -1, 1]  # AND gate
        >>> p.fit(X, y) is not None
        True
        >>> p.predict([[0, 0], [1, 1]])
        [-1, 1]
        """
        if self.random_state is not None:
            random.seed(self.random_state)
        
        n_samples = len(X)
        n_features = len(X[0])
        
        # Initialize weights
        self.weights = [0.0] * n_features
        self.bias = 0.0
        self.n_updates = 0
        
        for epoch in range(self.max_epochs):
            errors = 0
            
            # Shuffle indices
            indices = list(range(n_samples))
            random.shuffle(indices)
            
            for i in indices:
                # Compute prediction
                z = self._dot(self.weights, X[i]) + self.bias
                prediction = self._sign(z)
                
                # Update if misclassified
                if prediction != y[i]:
                    # w = w + η * y * x
                    self.weights = [
                        w + self.lr * y[i] * x
                        for w, x in zip(self.weights, X[i])
                    ]
                    # b = b + η * y
                    self.bias += self.lr * y[i]
                    
                    errors += 1
                    self.n_updates += 1
            
            # Converged
            if errors == 0:
                break
        
        return self
    
    def predict(self, X: List[List[float]]) -> List[int]:
        """
        Predict class labels.
        
        >>> p = Perceptron()
        >>> p.weights = [1.0, 1.0]
        >>> p.bias = -1.5
        >>> p.predict([[0, 0], [1, 1]])
        [-1, 1]
        """
        predictions = []
        for x in X:
            z = self._dot(self.weights, x) + self.bias
            predictions.append(self._sign(z))
        return predictions
    
    def decision_function(self, X: List[List[float]]) -> List[float]:
        """Return raw decision values."""
        return [self._dot(self.weights, x) + self.bias for x in X]
    
    def score(self, X: List[List[float]], y: List[int]) -> float:
        """
        Compute accuracy.
        
        >>> p = Perceptron()
        >>> p.weights = [1.0, 1.0]
        >>> p.bias = -1.5
        >>> p.score([[0, 0], [1, 1]], [-1, 1])
        1.0
        """
        predictions = self.predict(X)
        correct = sum(1 for p, yi in zip(predictions, y) if p == yi)
        return correct / len(y)


class AveragedPerceptron:
    """
    Averaged Perceptron for improved generalization.
    
    Returns average of all weight vectors during training.
    """
    
    def __init__(
        self,
        learning_rate: float = 1.0,
        max_epochs: int = 100
    ):
        self.lr = learning_rate
        self.max_epochs = max_epochs
        
        self.weights: Optional[List[float]] = None
        self.bias: float = 0.0
    
    def fit(
        self,
        X: List[List[float]],
        y: List[int]
    ) -> 'AveragedPerceptron':
        """
        Train averaged perceptron.
        
        >>> ap = AveragedPerceptron(max_epochs=50)
        >>> X = [[0, 0], [0, 1], [1, 0], [1, 1]]
        >>> y = [-1, -1, -1, 1]
        >>> ap.fit(X, y) is not None
        True
        """
        n_features = len(X[0])
        
        # Current weights
        w = [0.0] * n_features
        b = 0.0
        
        # Accumulated weights
        w_sum = [0.0] * n_features
        b_sum = 0.0
        count = 0
        
        for _ in range(self.max_epochs):
            for i in range(len(X)):
                z = sum(wi * xi for wi, xi in zip(w, X[i])) + b
                
                if (z >= 0 and y[i] < 0) or (z < 0 and y[i] > 0):
                    # Update
                    w = [wi + self.lr * y[i] * xi for wi, xi in zip(w, X[i])]
                    b = b + self.lr * y[i]
                
                # Accumulate
                w_sum = [ws + wi for ws, wi in zip(w_sum, w)]
                b_sum += b
                count += 1
        
        # Average
        self.weights = [ws / count for ws in w_sum]
        self.bias = b_sum / count
        
        return self
    
    def predict(self, X: List[List[float]]) -> List[int]:
        """Predict class labels."""
        predictions = []
        for x in X:
            z = sum(wi * xi for wi, xi in zip(self.weights, x)) + self.bias
            predictions.append(1 if z >= 0 else -1)
        return predictions


class VotedPerceptron:
    """
    Voted Perceptron for non-separable data.
    
    Maintains ensemble of perceptrons encountered during training.
    """
    
    def __init__(
        self,
        learning_rate: float = 1.0,
        max_epochs: int = 50
    ):
        self.lr = learning_rate
        self.max_epochs = max_epochs
        
        # List of (weights, bias, survival_count)
        self.voters: List[Tuple[List[float], float, int]] = []
    
    def fit(
        self,
        X: List[List[float]],
        y: List[int]
    ) -> 'VotedPerceptron':
        """
        Train voted perceptron.
        
        >>> vp = VotedPerceptron(max_epochs=20)
        >>> X = [[0, 0], [0, 1], [1, 0], [1, 1]]
        >>> y = [-1, -1, -1, 1]
        >>> vp.fit(X, y) is not None
        True
        >>> len(vp.voters) > 0
        True
        """
        n_features = len(X[0])
        
        w = [0.0] * n_features
        b = 0.0
        c = 0  # Survival count
        
        self.voters = []
        
        for _ in range(self.max_epochs):
            for i in range(len(X)):
                z = sum(wi * xi for wi, xi in zip(w, X[i])) + b
                prediction = 1 if z >= 0 else -1
                
                if prediction != y[i]:
                    # Store current voter
                    if c > 0:
                        self.voters.append((w.copy(), b, c))
                    
                    # Update weights
                    w = [wi + self.lr * y[i] * xi for wi, xi in zip(w, X[i])]
                    b = b + self.lr * y[i]
                    c = 1
                else:
                    c += 1
        
        # Add final voter
        if c > 0:
            self.voters.append((w, b, c))
        
        return self
    
    def predict(self, X: List[List[float]]) -> List[int]:
        """
        Predict using weighted vote.
        """
        predictions = []
        
        for x in X:
            vote_sum = 0
            
            for w, b, c in self.voters:
                z = sum(wi * xi for wi, xi in zip(w, x)) + b
                vote = 1 if z >= 0 else -1
                vote_sum += c * vote
            
            predictions.append(1 if vote_sum >= 0 else -1)
        
        return predictions


class KernelPerceptron:
    """
    Kernel Perceptron for non-linear boundaries.
    """
    
    def __init__(
        self,
        kernel: str = 'rbf',
        gamma: float = 1.0,
        degree: int = 3,
        max_epochs: int = 100
    ):
        """
        Initialize kernel perceptron.
        
        Args:
            kernel: 'linear', 'poly', or 'rbf'
            gamma: RBF/poly kernel parameter
            degree: Polynomial degree
        """
        self.kernel = kernel
        self.gamma = gamma
        self.degree = degree
        self.max_epochs = max_epochs
        
        self.X_train: Optional[List[List[float]]] = None
        self.y_train: Optional[List[int]] = None
        self.alphas: Optional[List[int]] = None
    
    def _kernel_func(self, x1: List[float], x2: List[float]) -> float:
        """Compute kernel."""
        if self.kernel == 'linear':
            return sum(a * b for a, b in zip(x1, x2))
        elif self.kernel == 'poly':
            dot = sum(a * b for a, b in zip(x1, x2))
            return (self.gamma * dot + 1) ** self.degree
        elif self.kernel == 'rbf':
            sq_dist = sum((a - b) ** 2 for a, b in zip(x1, x2))
            return math.exp(-self.gamma * sq_dist)
        else:
            raise ValueError(f"Unknown kernel: {self.kernel}")
    
    def fit(
        self,
        X: List[List[float]],
        y: List[int]
    ) -> 'KernelPerceptron':
        """
        Train kernel perceptron.
        
        >>> kp = KernelPerceptron(kernel='rbf', gamma=0.5)
        >>> # XOR problem
        >>> X = [[0, 0], [0, 1], [1, 0], [1, 1]]
        >>> y = [-1, 1, 1, -1]
        >>> kp.fit(X, y) is not None
        True
        """
        self.X_train = X
        self.y_train = y
        n = len(X)
        
        self.alphas = [0] * n
        
        for _ in range(self.max_epochs):
            errors = 0
            
            for i in range(n):
                # Compute decision
                f = 0.0
                for j in range(n):
                    if self.alphas[j] != 0:
                        f += self.alphas[j] * y[j] * self._kernel_func(X[j], X[i])
                
                prediction = 1 if f >= 0 else -1
                
                if prediction != y[i]:
                    self.alphas[i] += 1
                    errors += 1
            
            if errors == 0:
                break
        
        return self
    
    def predict(self, X: List[List[float]]) -> List[int]:
        """Predict using kernel."""
        predictions = []
        
        for x in X:
            f = 0.0
            for i in range(len(self.X_train)):
                if self.alphas[i] != 0:
                    f += self.alphas[i] * self.y_train[i] * self._kernel_func(self.X_train[i], x)
            
            predictions.append(1 if f >= 0 else -1)
        
        return predictions


class PocketPerceptron:
    """
    Pocket algorithm for non-separable data.
    
    Keeps track of best weights encountered during training.
    """
    
    def __init__(
        self,
        learning_rate: float = 1.0,
        max_epochs: int = 1000
    ):
        self.lr = learning_rate
        self.max_epochs = max_epochs
        
        self.weights: Optional[List[float]] = None
        self.bias: float = 0.0
    
    def fit(
        self,
        X: List[List[float]],
        y: List[int]
    ) -> 'PocketPerceptron':
        """
        Train pocket perceptron.
        """
        n_features = len(X[0])
        
        # Current weights
        w = [0.0] * n_features
        b = 0.0
        
        # Best weights (in pocket)
        best_w = w.copy()
        best_b = b
        best_errors = len(X)
        
        for _ in range(self.max_epochs):
            # Shuffle
            indices = list(range(len(X)))
            random.shuffle(indices)
            
            for i in indices:
                z = sum(wi * xi for wi, xi in zip(w, X[i])) + b
                prediction = 1 if z >= 0 else -1
                
                if prediction != y[i]:
                    # Update
                    w = [wi + self.lr * y[i] * xi for wi, xi in zip(w, X[i])]
                    b = b + self.lr * y[i]
                    
                    # Count errors
                    errors = sum(
                        1 for j in range(len(X))
                        if (1 if sum(wi * xi for wi, xi in zip(w, X[j])) + b >= 0 else -1) != y[j]
                    )
                    
                    # Update pocket
                    if errors < best_errors:
                        best_w = w.copy()
                        best_b = b
                        best_errors = errors
            
            if best_errors == 0:
                break
        
        self.weights = best_w
        self.bias = best_b
        
        return self
    
    def predict(self, X: List[List[float]]) -> List[int]:
        """Predict class labels."""
        predictions = []
        for x in X:
            z = sum(wi * xi for wi, xi in zip(self.weights, x)) + self.bias
            predictions.append(1 if z >= 0 else -1)
        return predictions


class MulticlassPerceptron:
    """
    Multiclass perceptron using one-vs-all strategy.
    """
    
    def __init__(
        self,
        learning_rate: float = 1.0,
        max_epochs: int = 100
    ):
        self.lr = learning_rate
        self.max_epochs = max_epochs
        
        self.classifiers: dict = {}
        self.classes: List[int] = []
    
    def fit(
        self,
        X: List[List[float]],
        y: List[int]
    ) -> 'MulticlassPerceptron':
        """
        Train one perceptron per class.
        """
        self.classes = list(set(y))
        
        for cls in self.classes:
            # One vs all
            y_binary = [1 if yi == cls else -1 for yi in y]
            
            p = Perceptron(
                learning_rate=self.lr,
                max_epochs=self.max_epochs
            )
            p.fit(X, y_binary)
            self.classifiers[cls] = p
        
        return self
    
    def predict(self, X: List[List[float]]) -> List[int]:
        """
        Predict class with highest score.
        """
        predictions = []
        
        for x in X:
            scores = {
                cls: p.decision_function([x])[0]
                for cls, p in self.classifiers.items()
            }
            predictions.append(max(scores, key=scores.get))
        
        return predictions
```

## 8. Applications

### 8.1 Common Use Cases

- Binary classification problems
- Online learning (streaming data)
- Text classification baseline
- Part-of-speech tagging
- Feature importance analysis

### 8.2 When to Use Perceptron

- Linearly separable data
- Simple baseline needed
- Online/incremental learning
- Need fast training

## 9. Real-World Software Engineering Applications

### 9.1 Production Example: Online Spam Filter

```python
from typing import List, Dict, Set, Optional, Tuple
import re
import math
from collections import defaultdict


class OnlineSpamFilter:
    """
    Online spam filter using averaged perceptron.
    
    Supports incremental updates as new emails arrive.
    """
    
    def __init__(
        self,
        learning_rate: float = 1.0,
        max_features: int = 10000
    ):
        """
        Initialize spam filter.
        
        Args:
            learning_rate: Update step size
            max_features: Maximum vocabulary size
        """
        self.lr = learning_rate
        self.max_features = max_features
        
        # Feature weights
        self.weights: Dict[str, float] = defaultdict(float)
        self.bias: float = 0.0
        
        # For averaging
        self.weight_sums: Dict[str, float] = defaultdict(float)
        self.bias_sum: float = 0.0
        self.update_count: int = 0
        
        # Vocabulary
        self.vocabulary: Set[str] = set()
        self.word_counts: Dict[str, int] = defaultdict(int)
        
        # Stats
        self.n_spam: int = 0
        self.n_ham: int = 0
    
    def _tokenize(self, text: str) -> List[str]:
        """Extract features from email text."""
        text = text.lower()
        
        # Basic tokens
        words = re.findall(r'\b[a-z]{2,}\b', text)
        
        # Bigrams
        bigrams = [
            f"{words[i]}_{words[i+1]}"
            for i in range(len(words) - 1)
        ]
        
        return words + bigrams
    
    def _extract_features(self, email: dict) -> Dict[str, float]:
        """
        Extract feature vector from email.
        
        >>> sf = OnlineSpamFilter()
        >>> features = sf._extract_features({'subject': 'Hello', 'body': 'Test message'})
        >>> 'hello' in features or 'test' in features
        True
        """
        features: Dict[str, float] = defaultdict(float)
        
        # Text features
        text = f"{email.get('subject', '')} {email.get('body', '')}"
        tokens = self._tokenize(text)
        
        for token in tokens:
            features[f"word:{token}"] += 1
        
        # Header features
        if email.get('from'):
            from_addr = email['from'].lower()
            features['from_contains_numbers'] = 1 if re.search(r'\d', from_addr) else 0
            features['from_length'] = len(from_addr) / 50
        
        # URL features
        urls = re.findall(r'https?://\S+', text)
        features['n_urls'] = len(urls)
        features['has_suspicious_url'] = 1 if any(
            domain in url for url in urls
            for domain in ['bit.ly', 'tinyurl', 'click']
        ) else 0
        
        # Special character features
        features['n_exclamation'] = text.count('!')
        features['n_dollar'] = text.count('$')
        features['all_caps_ratio'] = sum(1 for c in text if c.isupper()) / max(len(text), 1)
        
        # Spam keywords
        spam_keywords = ['free', 'winner', 'prize', 'urgent', 'click', 'offer', 'limited']
        features['n_spam_keywords'] = sum(1 for kw in spam_keywords if kw in text.lower())
        
        return features
    
    def _dot_product(self, features: Dict[str, float]) -> float:
        """Compute weighted sum."""
        total = self.bias
        for feature, value in features.items():
            if feature in self.weights:
                total += self.weights[feature] * value
        return total
    
    def partial_fit(
        self,
        emails: List[dict],
        labels: List[int]
    ) -> 'OnlineSpamFilter':
        """
        Incrementally update model with new examples.
        
        Args:
            emails: New email dictionaries
            labels: 1 for spam, -1 for ham
        """
        for email, label in zip(emails, labels):
            features = self._extract_features(email)
            
            # Update vocabulary
            for feature in features:
                if feature.startswith('word:'):
                    word = feature[5:]
                    self.word_counts[word] += 1
                    if len(self.vocabulary) < self.max_features:
                        self.vocabulary.add(word)
            
            # Prediction
            score = self._dot_product(features)
            prediction = 1 if score >= 0 else -1
            
            # Update if wrong
            if prediction != label:
                for feature, value in features.items():
                    self.weights[feature] += self.lr * label * value
                self.bias += self.lr * label
            
            # Accumulate for averaging
            for feature, weight in self.weights.items():
                self.weight_sums[feature] += weight
            self.bias_sum += self.bias
            self.update_count += 1
            
            # Update stats
            if label == 1:
                self.n_spam += 1
            else:
                self.n_ham += 1
        
        return self
    
    def predict(
        self,
        emails: List[dict],
        use_averaged: bool = True
    ) -> List[int]:
        """
        Classify emails as spam or ham.
        
        Args:
            emails: Emails to classify
            use_averaged: Use averaged weights
        
        Returns:
            1 for spam, -1 for ham.
        """
        predictions = []
        
        for email in emails:
            features = self._extract_features(email)
            
            if use_averaged and self.update_count > 0:
                # Use averaged weights
                score = self.bias_sum / self.update_count
                for feature, value in features.items():
                    if feature in self.weight_sums:
                        score += (self.weight_sums[feature] / self.update_count) * value
            else:
                score = self._dot_product(features)
            
            predictions.append(1 if score >= 0 else -1)
        
        return predictions
    
    def predict_proba(
        self,
        emails: List[dict]
    ) -> List[Tuple[float, float]]:
        """
        Return spam probability estimates.
        
        Uses sigmoid transformation of decision value.
        """
        probas = []
        
        for email in emails:
            features = self._extract_features(email)
            score = self._dot_product(features)
            
            # Sigmoid
            prob_spam = 1 / (1 + math.exp(-score))
            probas.append((1 - prob_spam, prob_spam))
        
        return probas
    
    def get_top_features(
        self,
        n: int = 20
    ) -> Dict[str, List[Tuple[str, float]]]:
        """
        Get most important features.
        """
        if self.update_count == 0:
            return {'spam': [], 'ham': []}
        
        # Use averaged weights
        avg_weights = {
            f: w / self.update_count
            for f, w in self.weight_sums.items()
        }
        
        sorted_features = sorted(
            avg_weights.items(),
            key=lambda x: x[1]
        )
        
        return {
            'spam': sorted_features[-n:][::-1],  # Highest weights
            'ham': sorted_features[:n]           # Lowest weights
        }
    
    def evaluate(
        self,
        emails: List[dict],
        labels: List[int]
    ) -> Dict[str, float]:
        """
        Compute evaluation metrics.
        """
        predictions = self.predict(emails)
        
        tp = sum(1 for p, y in zip(predictions, labels) if p == 1 and y == 1)
        tn = sum(1 for p, y in zip(predictions, labels) if p == -1 and y == -1)
        fp = sum(1 for p, y in zip(predictions, labels) if p == 1 and y == -1)
        fn = sum(1 for p, y in zip(predictions, labels) if p == -1 and y == 1)
        
        accuracy = (tp + tn) / len(labels)
        precision = tp / (tp + fp) if tp + fp > 0 else 0
        recall = tp / (tp + fn) if tp + fn > 0 else 0
        f1 = 2 * precision * recall / (precision + recall) if precision + recall > 0 else 0
        
        return {
            'accuracy': accuracy,
            'precision': precision,
            'recall': recall,
            'f1': f1,
            'spam_caught': tp,
            'ham_preserved': tn,
            'false_positives': fp,  # Ham marked as spam
            'false_negatives': fn   # Spam missed
        }
    
    def save_model(self) -> dict:
        """Serialize model for storage."""
        return {
            'weights': dict(self.weights),
            'bias': self.bias,
            'weight_sums': dict(self.weight_sums),
            'bias_sum': self.bias_sum,
            'update_count': self.update_count,
            'vocabulary': list(self.vocabulary),
            'n_spam': self.n_spam,
            'n_ham': self.n_ham
        }
    
    def load_model(self, data: dict) -> 'OnlineSpamFilter':
        """Load model from serialized data."""
        self.weights = defaultdict(float, data['weights'])
        self.bias = data['bias']
        self.weight_sums = defaultdict(float, data['weight_sums'])
        self.bias_sum = data['bias_sum']
        self.update_count = data['update_count']
        self.vocabulary = set(data['vocabulary'])
        self.n_spam = data['n_spam']
        self.n_ham = data['n_ham']
        return self


class POSTagger:
    """
    Part-of-Speech tagger using averaged perceptron.
    """
    
    def __init__(self, max_epochs: int = 5):
        self.max_epochs = max_epochs
        
        # Weight dict: {(tag, feature): weight}
        self.weights: Dict[Tuple[str, str], float] = defaultdict(float)
        self.weight_totals: Dict[Tuple[str, str], float] = defaultdict(float)
        self.timestamps: Dict[Tuple[str, str], int] = defaultdict(int)
        
        self.tags: Set[str] = set()
        self.iterations: int = 0
    
    def _extract_features(
        self,
        words: List[str],
        i: int,
        prev_tag: str,
        prev2_tag: str
    ) -> List[str]:
        """Extract features for word at position i."""
        word = words[i]
        
        features = [
            f"bias",
            f"word={word}",
            f"word_lower={word.lower()}",
            f"suffix3={word[-3:]}",
            f"suffix2={word[-2:]}",
            f"prefix3={word[:3]}",
            f"prev_tag={prev_tag}",
            f"prev2_tag={prev2_tag}",
            f"prev_tag+tag={prev_tag}+{prev2_tag}",
        ]
        
        # Shape features
        if word.isupper():
            features.append("all_caps")
        if word.istitle():
            features.append("titlecase")
        if word.isdigit():
            features.append("digit")
        if '-' in word:
            features.append("hyphen")
        
        # Context
        if i > 0:
            features.append(f"prev_word={words[i-1]}")
        if i < len(words) - 1:
            features.append(f"next_word={words[i+1]}")
        
        return features
    
    def _score(self, features: List[str], tag: str) -> float:
        """Score a tag given features."""
        return sum(self.weights[(tag, f)] for f in features)
    
    def _update(
        self,
        features: List[str],
        true_tag: str,
        pred_tag: str
    ):
        """Update weights for incorrect prediction."""
        if true_tag == pred_tag:
            return
        
        for f in features:
            self.weights[(true_tag, f)] += 1
            self.weights[(pred_tag, f)] -= 1
    
    def fit(
        self,
        sentences: List[List[Tuple[str, str]]]
    ) -> 'POSTagger':
        """
        Train POS tagger.
        
        Args:
            sentences: List of [(word, tag), ...] sequences
        """
        for sentence in sentences:
            for word, tag in sentence:
                self.tags.add(tag)
        
        for epoch in range(self.max_epochs):
            random.shuffle(sentences)
            
            for sentence in sentences:
                words = [w for w, _ in sentence]
                tags = [t for _, t in sentence]
                
                prev_tag = '<START>'
                prev2_tag = '<START>'
                
                for i in range(len(words)):
                    features = self._extract_features(words, i, prev_tag, prev2_tag)
                    
                    # Find best tag
                    scores = {
                        tag: self._score(features, tag)
                        for tag in self.tags
                    }
                    pred_tag = max(scores, key=scores.get)
                    
                    # Update
                    self._update(features, tags[i], pred_tag)
                    
                    prev2_tag = prev_tag
                    prev_tag = pred_tag
                    self.iterations += 1
        
        return self
    
    def predict(self, words: List[str]) -> List[str]:
        """Predict tags for words."""
        tags = []
        prev_tag = '<START>'
        prev2_tag = '<START>'
        
        for i in range(len(words)):
            features = self._extract_features(words, i, prev_tag, prev2_tag)
            
            scores = {
                tag: self._score(features, tag)
                for tag in self.tags
            }
            pred_tag = max(scores, key=scores.get)
            
            tags.append(pred_tag)
            prev2_tag = prev_tag
            prev_tag = pred_tag
        
        return tags


# Demo
if __name__ == "__main__":
    print("Perceptron Demo")
    print("=" * 50)
    
    # AND gate
    print("\n1. AND Gate Learning:")
    X = [[0, 0], [0, 1], [1, 0], [1, 1]]
    y = [-1, -1, -1, 1]
    
    p = Perceptron(learning_rate=1.0, max_epochs=100)
    p.fit(X, y)
    
    print(f"   Weights: {p.weights}")
    print(f"   Bias: {p.bias}")
    print(f"   Updates: {p.n_updates}")
    print(f"   Accuracy: {p.score(X, y):.2%}")
    
    # XOR with kernel
    print("\n2. XOR with Kernel Perceptron:")
    X_xor = [[0, 0], [0, 1], [1, 0], [1, 1]]
    y_xor = [-1, 1, 1, -1]
    
    kp = KernelPerceptron(kernel='rbf', gamma=1.0, max_epochs=100)
    kp.fit(X_xor, y_xor)
    
    predictions = kp.predict(X_xor)
    accuracy = sum(1 for p, yi in zip(predictions, y_xor) if p == yi) / len(y_xor)
    print(f"   Predictions: {predictions}")
    print(f"   Accuracy: {accuracy:.2%}")
    
    # Online spam filter
    print("\n3. Online Spam Filter:")
    spam_filter = OnlineSpamFilter()
    
    # Training emails
    train_emails = [
        {'subject': 'Meeting tomorrow', 'body': 'Let\'s discuss the project'},
        {'subject': 'FREE PRIZE!!!', 'body': 'Click here to claim $1000'},
        {'subject': 'Lunch?', 'body': 'Want to grab lunch today?'},
        {'subject': 'URGENT: Winner selected', 'body': 'You have won! Click now!'},
    ]
    train_labels = [-1, 1, -1, 1]  # -1=ham, 1=spam
    
    spam_filter.partial_fit(train_emails, train_labels)
    
    # Test
    test_emails = [
        {'subject': 'Project update', 'body': 'Here is the latest status'},
        {'subject': 'FREE money!!!', 'body': 'Click to receive prize $$$'},
    ]
    
    predictions = spam_filter.predict(test_emails)
    print(f"   Test predictions: {['SPAM' if p == 1 else 'HAM' for p in predictions]}")
```

## 10. Comparison with Other Classifiers

| Aspect | Perceptron | Logistic Regression | SVM |
|--------|------------|---------------------|-----|
| Output | Binary | Probability | Margin |
| Non-separable | Fails | Handles | Handles |
| Online learning | Native | Possible | Difficult |
| Training | Fast | Moderate | Slow |
| Convergence | If separable | Always | Always |

## 11. Best Practices

1. **Normalize features** for faster convergence
2. **Use averaged perceptron** for better generalization
3. **Shuffle data** each epoch
4. **Start with perceptron** as baseline
5. **Consider kernel** for non-linear data
6. **Monitor convergence** - no convergence = not separable

## 12. References

- [Wikipedia: Perceptron](https://en.wikipedia.org/wiki/Perceptron)
- Rosenblatt, F. "The Perceptron: A Probabilistic Model" (1958)
- Freund, Y. and Schapire, R. "Large Margin Classification Using the Perceptron Algorithm" (1999)
- Collins, M. "Discriminative Training Methods for HMMs" (2002)
