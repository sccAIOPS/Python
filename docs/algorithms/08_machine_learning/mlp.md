# Multi-Layer Perceptron (MLP) / Neural Network

## Overview
- **Category**: Supervised Learning / Deep Learning
- **Complexity**: Time: O(nhi²e) | Space: O(hi²)
- **Type**: Universal function approximator
- **Source Files**: [neural_network/](../../../neural_network/)

## 1. Mathematical Foundation

### 1.1 Network Architecture

An MLP consists of:
- **Input layer**: $d$ neurons (features)
- **Hidden layers**: $L-1$ layers with $h_l$ neurons each
- **Output layer**: $K$ neurons (classes or outputs)

### 1.2 Forward Propagation

For each layer $l$:

$$
\mathbf{z}^{(l)} = \mathbf{W}^{(l)}\mathbf{a}^{(l-1)} + \mathbf{b}^{(l)}
$$
$$
\mathbf{a}^{(l)} = \sigma(\mathbf{z}^{(l)})
$$

where:
- $\mathbf{W}^{(l)}$ = weight matrix
- $\mathbf{b}^{(l)}$ = bias vector
- $\sigma$ = activation function
- $\mathbf{a}^{(0)} = \mathbf{x}$ (input)

### 1.3 Activation Functions

**ReLU** (most common):
$$
\text{ReLU}(z) = \max(0, z)
$$

**Sigmoid**:
$$
\sigma(z) = \frac{1}{1 + e^{-z}}
$$

**Tanh**:
$$
\tanh(z) = \frac{e^z - e^{-z}}{e^z + e^{-z}}
$$

**Softmax** (output layer for classification):
$$
\text{softmax}(z_i) = \frac{e^{z_i}}{\sum_{j=1}^{K} e^{z_j}}
$$

**Leaky ReLU**:
$$
\text{LeakyReLU}(z) = \max(\alpha z, z), \quad \alpha \approx 0.01
$$

### 1.4 Loss Functions

**Mean Squared Error** (regression):
$$
L = \frac{1}{n}\sum_{i=1}^{n}\|\mathbf{y}_i - \hat{\mathbf{y}}_i\|^2
$$

**Cross-Entropy** (classification):
$$
L = -\frac{1}{n}\sum_{i=1}^{n}\sum_{k=1}^{K} y_{ik} \log(\hat{y}_{ik})
$$

**Binary Cross-Entropy**:
$$
L = -\frac{1}{n}\sum_{i=1}^{n}[y_i\log(\hat{y}_i) + (1-y_i)\log(1-\hat{y}_i)]
$$

### 1.5 Backpropagation

**Output layer error**:
$$
\boldsymbol{\delta}^{(L)} = \nabla_{\mathbf{a}^{(L)}}L \odot \sigma'(\mathbf{z}^{(L)})
$$

**Hidden layer error** (backward):
$$
\boldsymbol{\delta}^{(l)} = ((\mathbf{W}^{(l+1)})^T\boldsymbol{\delta}^{(l+1)}) \odot \sigma'(\mathbf{z}^{(l)})
$$

**Gradients**:
$$
\frac{\partial L}{\partial \mathbf{W}^{(l)}} = \boldsymbol{\delta}^{(l)}(\mathbf{a}^{(l-1)})^T
$$
$$
\frac{\partial L}{\partial \mathbf{b}^{(l)}} = \boldsymbol{\delta}^{(l)}
$$

### 1.6 Universal Approximation Theorem

A single hidden layer MLP with sufficient neurons can approximate any continuous function on a compact subset of $\mathbb{R}^n$.

## 2. Regularization Techniques

### 2.1 L2 Regularization (Weight Decay)

$$
L_{reg} = L + \frac{\lambda}{2}\sum_{l}\|\mathbf{W}^{(l)}\|_F^2
$$

### 2.2 Dropout

During training, randomly set activations to zero:
$$
\tilde{\mathbf{a}}^{(l)} = \mathbf{a}^{(l)} \odot \mathbf{m}, \quad m_i \sim \text{Bernoulli}(p)
$$

At test time, scale: $\mathbf{a}_{test} = p \cdot \mathbf{a}$

### 2.3 Batch Normalization

Normalize activations within mini-batch:
$$
\hat{x}_i = \frac{x_i - \mu_B}{\sqrt{\sigma_B^2 + \epsilon}}
$$
$$
y_i = \gamma\hat{x}_i + \beta
$$

## 3. Pseudocode

```
ALGORITHM ForwardPass(X, weights, biases, activations)
    INPUT: Input X, network parameters
    OUTPUT: Output prediction, cached values
    
    cache ← []
    a ← X
    
    for l ← 1 to L do
        z ← W[l] × a + b[l]
        a ← activation[l](z)
        cache.append((z, a))
    
    return a, cache

ALGORITHM BackwardPass(y, y_hat, cache, weights)
    INPUT: True labels, predictions, cached values, weights
    OUTPUT: Gradients for all parameters
    
    gradients ← {}
    
    // Output layer
    δ ← loss_derivative(y, y_hat) × activation_derivative(cache[L].z)
    gradients[W[L]] ← δ × cache[L-1].a.T
    gradients[b[L]] ← sum(δ, axis=1)
    
    // Hidden layers (backward)
    for l ← L-1 down to 1 do
        δ ← (W[l+1].T × δ) × activation_derivative(cache[l].z)
        gradients[W[l]] ← δ × cache[l-1].a.T
        gradients[b[l]] ← sum(δ, axis=1)
    
    return gradients

ALGORITHM TrainMLP(X, y, architecture, epochs, batch_size, η)
    INPUT: Data, labels, network config, hyperparameters
    OUTPUT: Trained weights and biases
    
    // Initialize weights (Xavier/He initialization)
    weights, biases ← InitializeWeights(architecture)
    
    for epoch ← 1 to epochs do
        shuffle(X, y)
        
        for batch in get_batches(X, y, batch_size) do
            X_batch, y_batch ← batch
            
            // Forward pass
            y_hat, cache ← ForwardPass(X_batch, weights, biases)
            
            // Compute loss
            loss ← ComputeLoss(y_batch, y_hat)
            
            // Backward pass
            gradients ← BackwardPass(y_batch, y_hat, cache, weights)
            
            // Update weights (gradient descent)
            for l ← 1 to L do
                W[l] ← W[l] - η × gradients[W[l]]
                b[l] ← b[l] - η × gradients[b[l]]
    
    return weights, biases

ALGORITHM Dropout(a, p, training)
    INPUT: Activations, keep probability, training mode
    OUTPUT: Dropped activations
    
    if not training then
        return a × p
    
    mask ← random(shape=a.shape) < p
    return a × mask / p  // Inverted dropout
```

## 4. Step-by-Step Example

### Example: XOR Problem (2-2-1 Network)

**Network**: 2 inputs → 2 hidden → 1 output

**Initial weights** (random):
- $W^{(1)} = \begin{bmatrix} 0.5 & 0.5 \\ 0.5 & 0.5 \end{bmatrix}$
- $b^{(1)} = \begin{bmatrix} 0 \\ 0 \end{bmatrix}$
- $W^{(2)} = \begin{bmatrix} 0.5 & 0.5 \end{bmatrix}$
- $b^{(2)} = \begin{bmatrix} 0 \end{bmatrix}$

**Forward pass for x = [0, 1]**:

1. Hidden layer:
   - $z^{(1)} = W^{(1)}x + b^{(1)} = \begin{bmatrix} 0.5 \\ 0.5 \end{bmatrix}$
   - $a^{(1)} = \sigma(z^{(1)}) = \begin{bmatrix} 0.622 \\ 0.622 \end{bmatrix}$

2. Output layer:
   - $z^{(2)} = W^{(2)}a^{(1)} + b^{(2)} = 0.622$
   - $\hat{y} = \sigma(0.622) = 0.651$

**Target**: y = 1 (XOR of 0,1)

**Loss**: $L = -(1 \cdot \log(0.651)) = 0.429$

**Backpropagation** updates weights to reduce loss...

## 5. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Forward (per sample) | O(Σh_l × h_{l-1}) | O(Σh_l) |
| Backward (per sample) | O(Σh_l × h_{l-1}) | O(Σh_l) |
| Epoch (n samples) | O(n × Σh_l × h_{l-1}) | O(Σh_l) |
| Full training | O(n × e × Σh_l × h_{l-1}) | O(Σh_l × h_{l-1}) |

For fully connected: ~O(nhi²e) time, O(hi²) parameters.

## 6. Visual Representation

### 6.1 MLP Architecture

```
Input Layer      Hidden Layer 1     Hidden Layer 2      Output Layer
    
   x₁ ●─────────────●─────────────────●
        ╲         ╱ ╲               ╱   ╲
   x₂ ●───────────●───────────────●─────●───→ ŷ
        ╲       ╱   ╲           ╱     ╱
   x₃ ●─────────●─────────────●─────
              ╱   ╲         ╱
   x₄ ●─────────────●─────────
   
   [d=4]        [h₁=3]          [h₂=2]        [K=1]
```

### 6.2 Single Neuron

```
         w₁
    x₁ ────╲
            ╲
         w₂  ╲
    x₂ ───────[Σ]───[σ]───→ output
            ╱
         w₃╱
    x₃ ────
            ↑
           [b]
    
    output = σ(w₁x₁ + w₂x₂ + w₃x₃ + b)
```

### 6.3 Activation Functions

```
ReLU:           Sigmoid:         Tanh:
   │  /            │    ___         │     ___
   │ /           ──┼───╱          ──┼────╱
───┼─────       ╱  │            ╲   │
   │         ──╱───┼──            ╲─┼──
   │                               ╲│
```

## 7. Implementation

```python
from typing import List, Tuple, Optional, Callable, Dict
import math
import random


def sigmoid(z: float) -> float:
    """Sigmoid activation."""
    return 1 / (1 + math.exp(-max(-500, min(500, z))))


def sigmoid_derivative(z: float) -> float:
    """Derivative of sigmoid."""
    s = sigmoid(z)
    return s * (1 - s)


def relu(z: float) -> float:
    """ReLU activation."""
    return max(0, z)


def relu_derivative(z: float) -> float:
    """Derivative of ReLU."""
    return 1 if z > 0 else 0


def tanh(z: float) -> float:
    """Tanh activation."""
    return math.tanh(z)


def tanh_derivative(z: float) -> float:
    """Derivative of tanh."""
    t = tanh(z)
    return 1 - t * t


def softmax(z: List[float]) -> List[float]:
    """Softmax activation."""
    max_z = max(z)
    exp_z = [math.exp(zi - max_z) for zi in z]
    sum_exp = sum(exp_z)
    return [e / sum_exp for e in exp_z]


class Layer:
    """Neural network layer."""
    
    def __init__(
        self,
        input_size: int,
        output_size: int,
        activation: str = 'relu'
    ):
        """
        Initialize layer.
        
        Args:
            input_size: Number of input neurons
            output_size: Number of output neurons
            activation: 'relu', 'sigmoid', 'tanh', or 'linear'
        """
        self.input_size = input_size
        self.output_size = output_size
        self.activation = activation
        
        # Initialize weights (He initialization for ReLU)
        if activation == 'relu':
            scale = math.sqrt(2.0 / input_size)
        else:
            scale = math.sqrt(1.0 / input_size)
        
        self.weights = [
            [random.gauss(0, scale) for _ in range(input_size)]
            for _ in range(output_size)
        ]
        self.biases = [0.0] * output_size
        
        # Cached values for backprop
        self.z: List[float] = []
        self.a: List[float] = []
        self.input: List[float] = []
        
        # Gradients
        self.grad_w: List[List[float]] = []
        self.grad_b: List[float] = []
    
    def _activate(self, z: float) -> float:
        """Apply activation function."""
        if self.activation == 'relu':
            return relu(z)
        elif self.activation == 'sigmoid':
            return sigmoid(z)
        elif self.activation == 'tanh':
            return tanh(z)
        else:
            return z
    
    def _activate_derivative(self, z: float) -> float:
        """Activation derivative."""
        if self.activation == 'relu':
            return relu_derivative(z)
        elif self.activation == 'sigmoid':
            return sigmoid_derivative(z)
        elif self.activation == 'tanh':
            return tanh_derivative(z)
        else:
            return 1.0
    
    def forward(self, x: List[float]) -> List[float]:
        """
        Forward pass through layer.
        
        >>> layer = Layer(2, 3, 'relu')
        >>> output = layer.forward([1.0, 2.0])
        >>> len(output)
        3
        """
        self.input = x
        self.z = []
        self.a = []
        
        for j in range(self.output_size):
            z_j = sum(
                self.weights[j][i] * x[i]
                for i in range(self.input_size)
            ) + self.biases[j]
            self.z.append(z_j)
            self.a.append(self._activate(z_j))
        
        return self.a
    
    def backward(self, delta: List[float]) -> List[float]:
        """
        Backward pass through layer.
        
        Returns delta for previous layer.
        """
        # Gradient w.r.t weights and biases
        self.grad_w = [
            [delta[j] * self.input[i] for i in range(self.input_size)]
            for j in range(self.output_size)
        ]
        self.grad_b = delta.copy()
        
        # Delta for previous layer
        prev_delta = [
            sum(
                self.weights[j][i] * delta[j] * self._activate_derivative(self.z[j])
                for j in range(self.output_size)
            )
            for i in range(self.input_size)
        ]
        
        return prev_delta
    
    def update(self, learning_rate: float, l2_lambda: float = 0.0):
        """Update weights using gradients."""
        for j in range(self.output_size):
            for i in range(self.input_size):
                # L2 regularization
                reg = l2_lambda * self.weights[j][i]
                self.weights[j][i] -= learning_rate * (self.grad_w[j][i] + reg)
            self.biases[j] -= learning_rate * self.grad_b[j]


class MLP:
    """
    Multi-Layer Perceptron neural network.
    """
    
    def __init__(
        self,
        layer_sizes: List[int],
        activation: str = 'relu',
        output_activation: str = 'sigmoid',
        learning_rate: float = 0.01,
        l2_lambda: float = 0.0
    ):
        """
        Initialize MLP.
        
        Args:
            layer_sizes: [input_size, hidden1, hidden2, ..., output_size]
            activation: Activation for hidden layers
            output_activation: Activation for output layer
            learning_rate: Learning rate for gradient descent
            l2_lambda: L2 regularization strength
        """
        self.layer_sizes = layer_sizes
        self.lr = learning_rate
        self.l2_lambda = l2_lambda
        
        # Create layers
        self.layers: List[Layer] = []
        for i in range(len(layer_sizes) - 1):
            act = output_activation if i == len(layer_sizes) - 2 else activation
            self.layers.append(Layer(layer_sizes[i], layer_sizes[i + 1], act))
    
    def forward(self, x: List[float]) -> List[float]:
        """
        Forward pass through network.
        
        >>> mlp = MLP([2, 4, 1])
        >>> output = mlp.forward([1.0, 2.0])
        >>> len(output)
        1
        """
        for layer in self.layers:
            x = layer.forward(x)
        return x
    
    def backward(self, y: List[float], y_hat: List[float]):
        """
        Backward pass to compute gradients.
        """
        # Output layer delta (assuming cross-entropy with sigmoid or MSE)
        delta = [y_hat[i] - y[i] for i in range(len(y))]
        
        # Backpropagate through layers
        for layer in reversed(self.layers):
            delta = layer.backward(delta)
    
    def update(self):
        """Update all weights."""
        for layer in self.layers:
            layer.update(self.lr, self.l2_lambda)
    
    def fit(
        self,
        X: List[List[float]],
        y: List[List[float]],
        epochs: int = 100,
        batch_size: int = 32,
        verbose: bool = False
    ) -> List[float]:
        """
        Train the network.
        
        Args:
            X: Training features
            y: Training targets (one-hot for classification)
            epochs: Number of training epochs
            batch_size: Mini-batch size
            verbose: Print progress
        
        Returns:
            Loss history.
        """
        n = len(X)
        losses = []
        
        for epoch in range(epochs):
            # Shuffle data
            indices = list(range(n))
            random.shuffle(indices)
            
            epoch_loss = 0.0
            
            for i in range(0, n, batch_size):
                batch_indices = indices[i:i + batch_size]
                batch_loss = 0.0
                
                for idx in batch_indices:
                    # Forward pass
                    y_hat = self.forward(X[idx])
                    
                    # Compute loss
                    loss = self._compute_loss(y[idx], y_hat)
                    batch_loss += loss
                    
                    # Backward pass
                    self.backward(y[idx], y_hat)
                
                # Average gradients and update
                for layer in self.layers:
                    layer.grad_w = [
                        [g / len(batch_indices) for g in row]
                        for row in layer.grad_w
                    ]
                    layer.grad_b = [g / len(batch_indices) for g in layer.grad_b]
                
                self.update()
                epoch_loss += batch_loss
            
            avg_loss = epoch_loss / n
            losses.append(avg_loss)
            
            if verbose and (epoch + 1) % 10 == 0:
                print(f"Epoch {epoch + 1}/{epochs}, Loss: {avg_loss:.6f}")
        
        return losses
    
    def _compute_loss(self, y: List[float], y_hat: List[float]) -> float:
        """Compute cross-entropy or MSE loss."""
        loss = 0.0
        for yi, yhi in zip(y, y_hat):
            if self.layers[-1].activation == 'sigmoid':
                # Binary cross-entropy
                yhi = max(1e-15, min(1 - 1e-15, yhi))
                loss -= yi * math.log(yhi) + (1 - yi) * math.log(1 - yhi)
            else:
                # MSE
                loss += (yi - yhi) ** 2
        return loss / len(y)
    
    def predict(self, X: List[List[float]]) -> List[List[float]]:
        """
        Predict outputs.
        
        >>> mlp = MLP([2, 4, 1])
        >>> outputs = mlp.predict([[1.0, 2.0], [3.0, 4.0]])
        >>> len(outputs)
        2
        """
        return [self.forward(x) for x in X]
    
    def predict_classes(self, X: List[List[float]]) -> List[int]:
        """Predict class labels (for classification)."""
        outputs = self.predict(X)
        
        if len(outputs[0]) == 1:
            # Binary classification
            return [1 if o[0] >= 0.5 else 0 for o in outputs]
        else:
            # Multiclass
            return [max(range(len(o)), key=lambda i: o[i]) for o in outputs]
    
    def score(
        self,
        X: List[List[float]],
        y: List[List[float]]
    ) -> float:
        """
        Compute accuracy.
        """
        predictions = self.predict_classes(X)
        
        if len(y[0]) == 1:
            true_labels = [int(yi[0]) for yi in y]
        else:
            true_labels = [max(range(len(yi)), key=lambda i: yi[i]) for yi in y]
        
        correct = sum(1 for p, t in zip(predictions, true_labels) if p == t)
        return correct / len(y)


class MLPClassifier:
    """
    MLP wrapper for classification tasks.
    """
    
    def __init__(
        self,
        hidden_layer_sizes: Tuple[int, ...] = (100,),
        activation: str = 'relu',
        learning_rate: float = 0.001,
        max_iter: int = 200,
        batch_size: int = 32,
        l2_lambda: float = 0.0001
    ):
        """
        Initialize classifier.
        
        Args:
            hidden_layer_sizes: Tuple of hidden layer sizes
            activation: Hidden layer activation
            learning_rate: Learning rate
            max_iter: Maximum epochs
            batch_size: Mini-batch size
            l2_lambda: L2 regularization
        """
        self.hidden_layer_sizes = hidden_layer_sizes
        self.activation = activation
        self.lr = learning_rate
        self.max_iter = max_iter
        self.batch_size = batch_size
        self.l2_lambda = l2_lambda
        
        self.mlp: Optional[MLP] = None
        self.classes: List[int] = []
        self.n_features: int = 0
    
    def _one_hot(self, labels: List[int]) -> List[List[float]]:
        """Convert labels to one-hot encoding."""
        n_classes = len(self.classes)
        one_hot = []
        
        for label in labels:
            vec = [0.0] * n_classes
            vec[self.classes.index(label)] = 1.0
            one_hot.append(vec)
        
        return one_hot
    
    def fit(
        self,
        X: List[List[float]],
        y: List[int]
    ) -> 'MLPClassifier':
        """
        Train classifier.
        
        >>> clf = MLPClassifier(hidden_layer_sizes=(4,), max_iter=100)
        >>> X = [[0, 0], [0, 1], [1, 0], [1, 1]]
        >>> y = [0, 1, 1, 0]  # XOR
        >>> clf.fit(X, y) is not None
        True
        """
        self.classes = list(sorted(set(y)))
        self.n_features = len(X[0])
        
        # Build architecture
        layer_sizes = [self.n_features] + list(self.hidden_layer_sizes)
        
        if len(self.classes) == 2:
            layer_sizes.append(1)
            output_activation = 'sigmoid'
        else:
            layer_sizes.append(len(self.classes))
            output_activation = 'sigmoid'  # Use softmax in production
        
        self.mlp = MLP(
            layer_sizes=layer_sizes,
            activation=self.activation,
            output_activation=output_activation,
            learning_rate=self.lr,
            l2_lambda=self.l2_lambda
        )
        
        # Convert labels
        if len(self.classes) == 2:
            y_encoded = [[float(yi == self.classes[1])] for yi in y]
        else:
            y_encoded = self._one_hot(y)
        
        # Train
        self.mlp.fit(
            X, y_encoded,
            epochs=self.max_iter,
            batch_size=self.batch_size
        )
        
        return self
    
    def predict(self, X: List[List[float]]) -> List[int]:
        """Predict class labels."""
        outputs = self.mlp.predict(X)
        
        if len(self.classes) == 2:
            indices = [1 if o[0] >= 0.5 else 0 for o in outputs]
        else:
            indices = [max(range(len(o)), key=lambda i: o[i]) for o in outputs]
        
        return [self.classes[i] for i in indices]
    
    def predict_proba(self, X: List[List[float]]) -> List[List[float]]:
        """Return class probabilities."""
        outputs = self.mlp.predict(X)
        
        if len(self.classes) == 2:
            return [[1 - o[0], o[0]] for o in outputs]
        else:
            return outputs
    
    def score(
        self,
        X: List[List[float]],
        y: List[int]
    ) -> float:
        """Compute accuracy."""
        predictions = self.predict(X)
        correct = sum(1 for p, yi in zip(predictions, y) if p == yi)
        return correct / len(y)


class MLPRegressor:
    """
    MLP wrapper for regression tasks.
    """
    
    def __init__(
        self,
        hidden_layer_sizes: Tuple[int, ...] = (100,),
        activation: str = 'relu',
        learning_rate: float = 0.001,
        max_iter: int = 200,
        batch_size: int = 32
    ):
        self.hidden_layer_sizes = hidden_layer_sizes
        self.activation = activation
        self.lr = learning_rate
        self.max_iter = max_iter
        self.batch_size = batch_size
        
        self.mlp: Optional[MLP] = None
    
    def fit(
        self,
        X: List[List[float]],
        y: List[float]
    ) -> 'MLPRegressor':
        """Train regressor."""
        n_features = len(X[0])
        layer_sizes = [n_features] + list(self.hidden_layer_sizes) + [1]
        
        self.mlp = MLP(
            layer_sizes=layer_sizes,
            activation=self.activation,
            output_activation='linear',
            learning_rate=self.lr
        )
        
        y_formatted = [[yi] for yi in y]
        self.mlp.fit(X, y_formatted, epochs=self.max_iter, batch_size=self.batch_size)
        
        return self
    
    def predict(self, X: List[List[float]]) -> List[float]:
        """Predict values."""
        outputs = self.mlp.predict(X)
        return [o[0] for o in outputs]
```

## 8. Applications

### 8.1 Common Use Cases

- Image classification
- Natural language processing
- Speech recognition
- Recommendation systems
- Game playing (RL)
- Anomaly detection
- Time series forecasting

### 8.2 When to Use MLP

- Non-linear relationships in data
- Large datasets available
- Complex patterns to learn
- Sufficient computational resources

## 9. Real-World Software Engineering Applications

### 9.1 Production Example: Credit Risk Assessment

```python
from typing import List, Dict, Tuple, Optional
import math
import random
from datetime import datetime


class CreditRiskMLP:
    """
    Production MLP for credit risk assessment.
    """
    
    def __init__(
        self,
        hidden_layers: Tuple[int, ...] = (64, 32, 16),
        learning_rate: float = 0.001,
        dropout_rate: float = 0.2,
        l2_lambda: float = 0.001
    ):
        """
        Initialize credit risk model.
        
        Args:
            hidden_layers: Sizes of hidden layers
            learning_rate: Learning rate
            dropout_rate: Dropout probability
            l2_lambda: L2 regularization
        """
        self.hidden_layers = hidden_layers
        self.lr = learning_rate
        self.dropout_rate = dropout_rate
        self.l2_lambda = l2_lambda
        
        self.mlp: Optional[MLP] = None
        self.feature_names: List[str] = []
        
        # Normalization parameters
        self.feature_means: Dict[str, float] = {}
        self.feature_stds: Dict[str, float] = {}
        
        # Model metadata
        self.training_date: Optional[datetime] = None
        self.n_training_samples: int = 0
        self.validation_auc: float = 0.0
    
    def _extract_features(
        self,
        application: dict
    ) -> List[float]:
        """
        Extract features from loan application.
        """
        features = []
        
        # Income features
        income = application.get('annual_income', 0)
        features.append(income)
        features.append(math.log1p(income))
        
        # Debt features
        debt = application.get('total_debt', 0)
        features.append(debt)
        features.append(debt / max(income, 1))  # Debt-to-income
        
        # Credit features
        features.append(application.get('credit_score', 650))
        features.append(application.get('credit_history_years', 0))
        features.append(application.get('num_credit_lines', 0))
        features.append(application.get('credit_utilization', 0.5))
        
        # Employment features
        features.append(application.get('employment_years', 0))
        features.append(1 if application.get('employment_type') == 'full_time' else 0)
        features.append(1 if application.get('employment_type') == 'self_employed' else 0)
        
        # Loan features
        loan_amount = application.get('loan_amount', 0)
        features.append(loan_amount)
        features.append(loan_amount / max(income, 1))  # Loan-to-income
        features.append(application.get('loan_term_months', 36) / 12)
        
        # Demographic features
        features.append(application.get('age', 35))
        features.append(1 if application.get('home_ownership') == 'own' else 0)
        features.append(1 if application.get('home_ownership') == 'mortgage' else 0)
        
        # Payment history
        features.append(application.get('num_late_payments', 0))
        features.append(application.get('months_since_delinquency', 100))
        
        return features
    
    def _normalize_features(
        self,
        X: List[List[float]],
        fit: bool = False
    ) -> List[List[float]]:
        """Standardize features."""
        if fit:
            n_features = len(X[0])
            
            for j in range(n_features):
                values = [X[i][j] for i in range(len(X))]
                self.feature_means[j] = sum(values) / len(values)
                variance = sum((v - self.feature_means[j]) ** 2 for v in values) / len(values)
                self.feature_stds[j] = math.sqrt(variance) if variance > 0 else 1.0
        
        normalized = []
        for x in X:
            norm_x = [
                (x[j] - self.feature_means.get(j, 0)) / self.feature_stds.get(j, 1)
                for j in range(len(x))
            ]
            normalized.append(norm_x)
        
        return normalized
    
    def fit(
        self,
        applications: List[dict],
        defaults: List[int],
        validation_split: float = 0.2,
        epochs: int = 100,
        batch_size: int = 32
    ) -> 'CreditRiskMLP':
        """
        Train credit risk model.
        
        Args:
            applications: Loan applications
            defaults: 1 if defaulted, 0 otherwise
            validation_split: Validation set fraction
            epochs: Training epochs
            batch_size: Mini-batch size
        """
        # Extract features
        X = [self._extract_features(app) for app in applications]
        
        # Split data
        n = len(X)
        n_val = int(n * validation_split)
        
        indices = list(range(n))
        random.shuffle(indices)
        
        train_idx = indices[n_val:]
        val_idx = indices[:n_val]
        
        X_train = [X[i] for i in train_idx]
        y_train = [defaults[i] for i in train_idx]
        X_val = [X[i] for i in val_idx]
        y_val = [defaults[i] for i in val_idx]
        
        # Normalize
        X_train = self._normalize_features(X_train, fit=True)
        X_val = self._normalize_features(X_val, fit=False)
        
        # Build network
        n_features = len(X_train[0])
        layer_sizes = [n_features] + list(self.hidden_layers) + [1]
        
        self.mlp = MLP(
            layer_sizes=layer_sizes,
            activation='relu',
            output_activation='sigmoid',
            learning_rate=self.lr,
            l2_lambda=self.l2_lambda
        )
        
        # Train
        y_train_formatted = [[float(y)] for y in y_train]
        self.mlp.fit(
            X_train, y_train_formatted,
            epochs=epochs,
            batch_size=batch_size
        )
        
        # Evaluate on validation
        y_val_pred = [self.mlp.forward(x)[0] for x in X_val]
        self.validation_auc = self._compute_auc(y_val, y_val_pred)
        
        # Metadata
        self.training_date = datetime.now()
        self.n_training_samples = len(X_train)
        
        return self
    
    def _compute_auc(
        self,
        y_true: List[int],
        y_score: List[float]
    ) -> float:
        """Compute AUC-ROC."""
        # Sort by score descending
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
    
    def predict_proba(
        self,
        applications: List[dict]
    ) -> List[float]:
        """
        Predict default probability.
        
        Returns:
            Probability of default for each application.
        """
        X = [self._extract_features(app) for app in applications]
        X = self._normalize_features(X, fit=False)
        
        probas = [self.mlp.forward(x)[0] for x in X]
        return probas
    
    def predict(
        self,
        applications: List[dict],
        threshold: float = 0.5
    ) -> List[int]:
        """
        Predict default (0 or 1).
        """
        probas = self.predict_proba(applications)
        return [1 if p >= threshold else 0 for p in probas]
    
    def assess_risk(
        self,
        application: dict
    ) -> Dict[str, any]:
        """
        Comprehensive risk assessment.
        
        >>> model = CreditRiskMLP()
        >>> # Would need training first
        >>> # result = model.assess_risk({'annual_income': 50000, ...})
        """
        proba = self.predict_proba([application])[0]
        
        # Risk tier
        if proba < 0.1:
            tier = 'LOW'
            recommendation = 'APPROVE'
        elif proba < 0.25:
            tier = 'MEDIUM-LOW'
            recommendation = 'APPROVE_WITH_CONDITIONS'
        elif proba < 0.5:
            tier = 'MEDIUM'
            recommendation = 'MANUAL_REVIEW'
        elif proba < 0.75:
            tier = 'MEDIUM-HIGH'
            recommendation = 'DECLINE_OR_HIGHER_RATE'
        else:
            tier = 'HIGH'
            recommendation = 'DECLINE'
        
        # Risk factors
        features = self._extract_features(application)
        risk_factors = []
        
        if application.get('credit_score', 650) < 600:
            risk_factors.append('Low credit score')
        if application.get('num_late_payments', 0) > 2:
            risk_factors.append('History of late payments')
        if features[3] > 0.4:  # Debt-to-income
            risk_factors.append('High debt-to-income ratio')
        if features[12] > 0.35:  # Loan-to-income
            risk_factors.append('High loan-to-income ratio')
        
        return {
            'default_probability': proba,
            'risk_tier': tier,
            'recommendation': recommendation,
            'risk_factors': risk_factors,
            'assessment_date': datetime.now().isoformat(),
            'model_version': '1.0',
            'model_auc': self.validation_auc
        }
    
    def get_model_info(self) -> dict:
        """Return model metadata."""
        return {
            'architecture': [len(self.feature_names)] + list(self.hidden_layers) + [1],
            'training_date': self.training_date.isoformat() if self.training_date else None,
            'n_training_samples': self.n_training_samples,
            'validation_auc': self.validation_auc,
            'learning_rate': self.lr,
            'dropout_rate': self.dropout_rate,
            'l2_lambda': self.l2_lambda
        }


class ImageClassifierMLP:
    """
    MLP for image classification (e.g., MNIST digits).
    """
    
    def __init__(
        self,
        input_size: int = 784,  # 28x28
        hidden_sizes: Tuple[int, ...] = (256, 128),
        n_classes: int = 10,
        learning_rate: float = 0.001
    ):
        self.input_size = input_size
        self.hidden_sizes = hidden_sizes
        self.n_classes = n_classes
        self.lr = learning_rate
        
        self.mlp: Optional[MLP] = None
    
    def _preprocess(self, images: List[List[float]]) -> List[List[float]]:
        """Normalize pixel values to [0, 1]."""
        return [[p / 255.0 for p in img] for img in images]
    
    def fit(
        self,
        images: List[List[float]],
        labels: List[int],
        epochs: int = 20,
        batch_size: int = 64
    ) -> 'ImageClassifierMLP':
        """Train on image data."""
        X = self._preprocess(images)
        
        # One-hot encode labels
        y = []
        for label in labels:
            one_hot = [0.0] * self.n_classes
            one_hot[label] = 1.0
            y.append(one_hot)
        
        # Build network
        layer_sizes = [self.input_size] + list(self.hidden_sizes) + [self.n_classes]
        
        self.mlp = MLP(
            layer_sizes=layer_sizes,
            activation='relu',
            output_activation='sigmoid',
            learning_rate=self.lr
        )
        
        self.mlp.fit(X, y, epochs=epochs, batch_size=batch_size, verbose=True)
        
        return self
    
    def predict(self, images: List[List[float]]) -> List[int]:
        """Predict digit labels."""
        X = self._preprocess(images)
        outputs = self.mlp.predict(X)
        return [max(range(len(o)), key=lambda i: o[i]) for o in outputs]


# Demo
if __name__ == "__main__":
    print("Multi-Layer Perceptron Demo")
    print("=" * 50)
    
    # XOR problem
    print("\n1. XOR Problem:")
    X_xor = [[0, 0], [0, 1], [1, 0], [1, 1]]
    y_xor = [[0], [1], [1], [0]]
    
    mlp = MLP(
        layer_sizes=[2, 8, 1],
        activation='relu',
        output_activation='sigmoid',
        learning_rate=0.1
    )
    
    losses = mlp.fit(X_xor, y_xor, epochs=1000, batch_size=4)
    
    predictions = mlp.predict(X_xor)
    print("   Predictions:")
    for x, y, p in zip(X_xor, y_xor, predictions):
        print(f"   {x} -> {p[0]:.3f} (target: {y[0]})")
    
    # Classification example
    print("\n2. Binary Classification:")
    clf = MLPClassifier(
        hidden_layer_sizes=(8, 4),
        activation='relu',
        learning_rate=0.1,
        max_iter=200
    )
    
    # Generate sample data
    random.seed(42)
    X_train = [[random.gauss(0, 1), random.gauss(0, 1)] for _ in range(100)]
    y_train = [1 if x[0] + x[1] > 0 else 0 for x in X_train]
    
    clf.fit(X_train, y_train)
    accuracy = clf.score(X_train, y_train)
    print(f"   Training accuracy: {accuracy:.2%}")
    
    # Credit risk example
    print("\n3. Credit Risk Assessment:")
    
    # Sample applications
    applications = [
        {'annual_income': 75000, 'total_debt': 15000, 'credit_score': 720,
         'employment_years': 5, 'loan_amount': 20000, 'num_late_payments': 0},
        {'annual_income': 35000, 'total_debt': 25000, 'credit_score': 580,
         'employment_years': 1, 'loan_amount': 15000, 'num_late_payments': 3},
    ]
    defaults = [0, 1]
    
    credit_model = CreditRiskMLP(hidden_layers=(16, 8))
    # Would train with: credit_model.fit(applications, defaults)
    print("   Credit risk model initialized")
    print("   (Would need more data for meaningful training)")
```

## 10. Comparison

| Aspect | MLP | Linear Models | Decision Trees |
|--------|-----|---------------|----------------|
| Non-linearity | Yes | No | Yes |
| Interpretability | Low | High | Medium |
| Training time | Slow | Fast | Medium |
| Data needed | Large | Small | Medium |
| Hyperparameters | Many | Few | Few |
| Overfitting risk | High | Low | High |

## 11. Best Practices

1. **Normalize inputs** to zero mean, unit variance
2. **Use ReLU** for hidden layers (faster training)
3. **Start simple**, add layers gradually
4. **Use dropout** to prevent overfitting
5. **Monitor validation loss** for early stopping
6. **Batch normalize** for deeper networks
7. **Try Adam optimizer** before SGD

## 12. References

- [Wikipedia: Multilayer Perceptron](https://en.wikipedia.org/wiki/Multilayer_perceptron)
- Rumelhart, D.E. et al. "Learning representations by back-propagating errors" (1986)
- Glorot, X. and Bengio, Y. "Understanding the difficulty of training deep feedforward neural networks" (2010)
- Srivastava, N. et al. "Dropout: A Simple Way to Prevent Neural Networks from Overfitting" (2014)
