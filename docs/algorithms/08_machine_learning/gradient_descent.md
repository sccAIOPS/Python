# Gradient Descent Optimization

## Overview
- **Category**: Optimization Algorithms
- **Complexity**: Time: O(ndi) per epoch | Space: O(d)
- **Type**: First-order iterative optimization
- **Source Files**: [machine_learning/gradient_descent.py](../../../machine_learning/gradient_descent.py)

## 1. Mathematical Foundation

### 1.1 The Optimization Problem

Minimize objective function $f(\mathbf{w})$:
$$
\mathbf{w}^* = \underset{\mathbf{w}}{\text{argmin}} \; f(\mathbf{w})
$$

### 1.2 Gradient Descent Update Rule

$$
\mathbf{w}_{t+1} = \mathbf{w}_t - \eta \nabla f(\mathbf{w}_t)
$$

where:
- $\eta$ = learning rate
- $\nabla f$ = gradient of objective function

### 1.3 Intuition

- Gradient points in direction of steepest ascent
- Negative gradient points toward local minimum
- Learning rate controls step size

### 1.4 Convergence Conditions

For convex $f$ with L-Lipschitz gradient:
$$
f(\mathbf{w}_T) - f(\mathbf{w}^*) \leq \frac{\|\mathbf{w}_0 - \mathbf{w}^*\|^2}{2\eta T}
$$

**Optimal learning rate**: $\eta = 1/L$

### 1.5 Gradient Descent Variants

| Variant | Update | Batch Size |
|---------|--------|------------|
| Batch GD | Full gradient | n (all data) |
| Stochastic GD | Single sample gradient | 1 |
| Mini-batch GD | Mini-batch gradient | B (e.g., 32) |

### 1.6 For Mean Squared Error

Loss function:
$$
L(\mathbf{w}) = \frac{1}{2n} \sum_{i=1}^{n} (y_i - \mathbf{w}^T\mathbf{x}_i)^2
$$

Gradient:
$$
\nabla L(\mathbf{w}) = \frac{1}{n} \sum_{i=1}^{n} (\mathbf{w}^T\mathbf{x}_i - y_i)\mathbf{x}_i = \frac{1}{n}\mathbf{X}^T(\mathbf{X}\mathbf{w} - \mathbf{y})
$$

## 2. Advanced Optimizers

### 2.1 Momentum

Accumulate velocity to accelerate convergence:

$$
\mathbf{v}_{t+1} = \gamma \mathbf{v}_t + \eta \nabla f(\mathbf{w}_t)
$$
$$
\mathbf{w}_{t+1} = \mathbf{w}_t - \mathbf{v}_{t+1}
$$

where $\gamma \approx 0.9$ is momentum coefficient.

### 2.2 Nesterov Accelerated Gradient (NAG)

Look-ahead gradient:
$$
\mathbf{v}_{t+1} = \gamma \mathbf{v}_t + \eta \nabla f(\mathbf{w}_t - \gamma \mathbf{v}_t)
$$
$$
\mathbf{w}_{t+1} = \mathbf{w}_t - \mathbf{v}_{t+1}
$$

### 2.3 AdaGrad

Adaptive learning rate per parameter:
$$
\mathbf{G}_t = \mathbf{G}_{t-1} + \nabla f(\mathbf{w}_t)^2
$$
$$
\mathbf{w}_{t+1} = \mathbf{w}_t - \frac{\eta}{\sqrt{\mathbf{G}_t + \epsilon}} \nabla f(\mathbf{w}_t)
$$

### 2.4 RMSprop

Exponential moving average of squared gradients:
$$
\mathbf{E}[g^2]_t = \rho \mathbf{E}[g^2]_{t-1} + (1-\rho) \nabla f(\mathbf{w}_t)^2
$$
$$
\mathbf{w}_{t+1} = \mathbf{w}_t - \frac{\eta}{\sqrt{\mathbf{E}[g^2]_t + \epsilon}} \nabla f(\mathbf{w}_t)
$$

### 2.5 Adam (Adaptive Moment Estimation)

Combines momentum and RMSprop:
$$
\mathbf{m}_t = \beta_1 \mathbf{m}_{t-1} + (1-\beta_1) \nabla f(\mathbf{w}_t)
$$
$$
\mathbf{v}_t = \beta_2 \mathbf{v}_{t-1} + (1-\beta_2) \nabla f(\mathbf{w}_t)^2
$$

Bias correction:
$$
\hat{\mathbf{m}}_t = \frac{\mathbf{m}_t}{1-\beta_1^t}, \quad \hat{\mathbf{v}}_t = \frac{\mathbf{v}_t}{1-\beta_2^t}
$$

Update:
$$
\mathbf{w}_{t+1} = \mathbf{w}_t - \frac{\eta}{\sqrt{\hat{\mathbf{v}}_t} + \epsilon} \hat{\mathbf{m}}_t
$$

Default: $\beta_1=0.9$, $\beta_2=0.999$, $\epsilon=10^{-8}$

## 3. Pseudocode

```
ALGORITHM GradientDescent(f, grad_f, w_init, η, max_iter)
    INPUT: Objective f, gradient grad_f, initial w, learning rate η
    OUTPUT: Optimized weights w
    
    w ← w_init
    
    for t ← 1 to max_iter do
        g ← grad_f(w)
        w ← w - η × g
        
        if ||g|| < tolerance then
            break
    
    return w

ALGORITHM SGD(data, grad_f, w_init, η, epochs)
    INPUT: Data, gradient function, initial w, learning rate, epochs
    OUTPUT: Optimized weights w
    
    w ← w_init
    
    for epoch ← 1 to epochs do
        shuffle(data)
        
        for each (x, y) in data do
            g ← grad_f(w, x, y)
            w ← w - η × g
    
    return w

ALGORITHM Adam(grad_f, w_init, η, β₁, β₂, ε, max_iter)
    INPUT: Gradient function, initial weights, hyperparameters
    OUTPUT: Optimized weights
    
    w ← w_init
    m ← zeros(|w|)  // First moment
    v ← zeros(|w|)  // Second moment
    
    for t ← 1 to max_iter do
        g ← grad_f(w)
        
        // Update biased moments
        m ← β₁ × m + (1 - β₁) × g
        v ← β₂ × v + (1 - β₂) × g²
        
        // Bias correction
        m_hat ← m / (1 - β₁^t)
        v_hat ← v / (1 - β₂^t)
        
        // Update weights
        w ← w - η × m_hat / (√v_hat + ε)
    
    return w
```

## 4. Step-by-Step Example

### Example: Minimizing f(w) = w² using GD

**Setup**: $f(w) = w^2$, $\nabla f(w) = 2w$, $\eta = 0.1$, $w_0 = 5$

| Iteration | $w$ | $\nabla f$ | $w_{new} = w - \eta \nabla f$ |
|-----------|-----|------------|------------------------------|
| 0 | 5.0 | 10.0 | 5 - 0.1×10 = 4.0 |
| 1 | 4.0 | 8.0 | 4 - 0.1×8 = 3.2 |
| 2 | 3.2 | 6.4 | 3.2 - 0.1×6.4 = 2.56 |
| 3 | 2.56 | 5.12 | 2.56 - 0.1×5.12 = 2.048 |
| ... | ... | ... | ... |
| 20 | 0.061 | 0.122 | 0.049 |

**Convergence**: $w \to 0$ as $t \to \infty$

## 5. Complexity Analysis

| Variant | Time per Epoch | Space |
|---------|---------------|-------|
| Batch GD | O(nd) | O(d) |
| SGD | O(d) | O(d) |
| Mini-batch | O(Bd) | O(d) |
| Momentum | O(nd) | O(2d) |
| Adam | O(nd) | O(3d) |

Where n = samples, d = dimensions, B = batch size.

## 6. Visual Representation

### 6.1 Gradient Descent Path

```
f(w) │
     │\                    /
     │ \                  /
     │  \                /
     │   \      ↓      /
     │    \    ↓    /
     │     \  ↓  /
     │      ╲↓╱  ← minimum
     │
     └────────●────────────→ w
              w*
```

### 6.2 Learning Rate Effects

```
Too Large:                 Too Small:              Just Right:
    ↗                          ↓                       ↘
   ↙ ↘                         ↓                        ↘
  ↗   ↙                        ↓                         ↘
 Diverges                      ↓                          ●
                          Very slow                   Converges
```

### 6.3 Momentum vs Vanilla GD

```
            Ravine:              Without Momentum:         With Momentum:
     ___________                     ↓↓↓↓                     ↘
    /           \                   ←→←→                       →
   /             \                   ↓↓                         →→
  /      ●        \              Oscillates                   Faster
 /                 \
```

## 7. Implementation

```python
from typing import List, Tuple, Callable, Optional
import math
import random


def gradient_descent(
    grad_func: Callable[[List[float]], List[float]],
    initial_weights: List[float],
    learning_rate: float = 0.01,
    max_iterations: int = 1000,
    tolerance: float = 1e-6
) -> Tuple[List[float], List[float]]:
    """
    Basic gradient descent optimization.
    
    >>> def grad_f(w): return [2 * w[0]]  # f(w) = w^2
    >>> w, losses = gradient_descent(grad_f, [5.0], learning_rate=0.1, max_iterations=100)
    >>> abs(w[0]) < 0.01
    True
    """
    weights = initial_weights.copy()
    losses = []
    
    for _ in range(max_iterations):
        gradient = grad_func(weights)
        
        # Compute gradient norm
        grad_norm = math.sqrt(sum(g ** 2 for g in gradient))
        losses.append(grad_norm)
        
        if grad_norm < tolerance:
            break
        
        # Update weights
        weights = [w - learning_rate * g for w, g in zip(weights, gradient)]
    
    return weights, losses


class SGD:
    """
    Stochastic Gradient Descent optimizer.
    """
    
    def __init__(
        self,
        learning_rate: float = 0.01,
        momentum: float = 0.0,
        nesterov: bool = False
    ):
        """
        Initialize SGD.
        
        Args:
            learning_rate: Step size
            momentum: Momentum coefficient (0 = no momentum)
            nesterov: Use Nesterov accelerated gradient
        """
        self.lr = learning_rate
        self.momentum = momentum
        self.nesterov = nesterov
        
        self.velocity: Optional[List[float]] = None
    
    def step(
        self,
        weights: List[float],
        gradient: List[float]
    ) -> List[float]:
        """
        Perform single optimization step.
        
        >>> sgd = SGD(learning_rate=0.1, momentum=0.9)
        >>> w = sgd.step([5.0], [10.0])
        >>> w[0] < 5.0
        True
        """
        if self.velocity is None:
            self.velocity = [0.0] * len(weights)
        
        if self.momentum > 0:
            # Update velocity
            self.velocity = [
                self.momentum * v + self.lr * g
                for v, g in zip(self.velocity, gradient)
            ]
            
            if self.nesterov:
                # Nesterov update
                weights = [
                    w - (self.momentum * v + self.lr * g)
                    for w, v, g in zip(weights, self.velocity, gradient)
                ]
            else:
                # Standard momentum
                weights = [w - v for w, v in zip(weights, self.velocity)]
        else:
            # Vanilla SGD
            weights = [w - self.lr * g for w, g in zip(weights, gradient)]
        
        return weights


class AdaGrad:
    """
    AdaGrad optimizer with adaptive learning rates.
    """
    
    def __init__(
        self,
        learning_rate: float = 0.01,
        epsilon: float = 1e-8
    ):
        self.lr = learning_rate
        self.epsilon = epsilon
        
        self.accumulated: Optional[List[float]] = None
    
    def step(
        self,
        weights: List[float],
        gradient: List[float]
    ) -> List[float]:
        """
        Perform AdaGrad step.
        
        >>> ada = AdaGrad(learning_rate=0.5)
        >>> w = ada.step([5.0], [10.0])
        >>> w[0] < 5.0
        True
        """
        if self.accumulated is None:
            self.accumulated = [0.0] * len(weights)
        
        # Accumulate squared gradients
        self.accumulated = [
            acc + g ** 2
            for acc, g in zip(self.accumulated, gradient)
        ]
        
        # Adaptive update
        weights = [
            w - self.lr * g / (math.sqrt(acc) + self.epsilon)
            for w, g, acc in zip(weights, gradient, self.accumulated)
        ]
        
        return weights


class RMSprop:
    """
    RMSprop optimizer.
    """
    
    def __init__(
        self,
        learning_rate: float = 0.01,
        rho: float = 0.9,
        epsilon: float = 1e-8
    ):
        """
        Initialize RMSprop.
        
        Args:
            learning_rate: Base learning rate
            rho: Decay rate for squared gradient average
            epsilon: Small constant for numerical stability
        """
        self.lr = learning_rate
        self.rho = rho
        self.epsilon = epsilon
        
        self.avg_sq_grad: Optional[List[float]] = None
    
    def step(
        self,
        weights: List[float],
        gradient: List[float]
    ) -> List[float]:
        """
        Perform RMSprop step.
        
        >>> rms = RMSprop(learning_rate=0.1)
        >>> w = rms.step([5.0], [10.0])
        >>> w[0] < 5.0
        True
        """
        if self.avg_sq_grad is None:
            self.avg_sq_grad = [0.0] * len(weights)
        
        # Update running average of squared gradients
        self.avg_sq_grad = [
            self.rho * avg + (1 - self.rho) * g ** 2
            for avg, g in zip(self.avg_sq_grad, gradient)
        ]
        
        # Update weights
        weights = [
            w - self.lr * g / (math.sqrt(avg) + self.epsilon)
            for w, g, avg in zip(weights, gradient, self.avg_sq_grad)
        ]
        
        return weights


class Adam:
    """
    Adam optimizer (Adaptive Moment Estimation).
    
    Combines momentum and RMSprop.
    """
    
    def __init__(
        self,
        learning_rate: float = 0.001,
        beta1: float = 0.9,
        beta2: float = 0.999,
        epsilon: float = 1e-8
    ):
        """
        Initialize Adam optimizer.
        
        Args:
            learning_rate: Step size
            beta1: Decay rate for first moment (momentum)
            beta2: Decay rate for second moment (RMSprop)
            epsilon: Small constant for numerical stability
        """
        self.lr = learning_rate
        self.beta1 = beta1
        self.beta2 = beta2
        self.epsilon = epsilon
        
        self.m: Optional[List[float]] = None  # First moment
        self.v: Optional[List[float]] = None  # Second moment
        self.t: int = 0  # Time step
    
    def step(
        self,
        weights: List[float],
        gradient: List[float]
    ) -> List[float]:
        """
        Perform Adam optimization step.
        
        >>> adam = Adam(learning_rate=0.1)
        >>> w = adam.step([5.0], [10.0])
        >>> w[0] < 5.0
        True
        """
        self.t += 1
        
        if self.m is None:
            self.m = [0.0] * len(weights)
            self.v = [0.0] * len(weights)
        
        # Update biased first moment estimate
        self.m = [
            self.beta1 * m + (1 - self.beta1) * g
            for m, g in zip(self.m, gradient)
        ]
        
        # Update biased second moment estimate
        self.v = [
            self.beta2 * v + (1 - self.beta2) * g ** 2
            for v, g in zip(self.v, gradient)
        ]
        
        # Bias correction
        m_hat = [m / (1 - self.beta1 ** self.t) for m in self.m]
        v_hat = [v / (1 - self.beta2 ** self.t) for v in self.v]
        
        # Update weights
        weights = [
            w - self.lr * m_h / (math.sqrt(v_h) + self.epsilon)
            for w, m_h, v_h in zip(weights, m_hat, v_hat)
        ]
        
        return weights
    
    def reset(self):
        """Reset optimizer state."""
        self.m = None
        self.v = None
        self.t = 0


class AdamW:
    """
    AdamW optimizer (Adam with decoupled weight decay).
    """
    
    def __init__(
        self,
        learning_rate: float = 0.001,
        beta1: float = 0.9,
        beta2: float = 0.999,
        epsilon: float = 1e-8,
        weight_decay: float = 0.01
    ):
        self.lr = learning_rate
        self.beta1 = beta1
        self.beta2 = beta2
        self.epsilon = epsilon
        self.weight_decay = weight_decay
        
        self.m: Optional[List[float]] = None
        self.v: Optional[List[float]] = None
        self.t: int = 0
    
    def step(
        self,
        weights: List[float],
        gradient: List[float]
    ) -> List[float]:
        """Perform AdamW step with decoupled weight decay."""
        self.t += 1
        
        if self.m is None:
            self.m = [0.0] * len(weights)
            self.v = [0.0] * len(weights)
        
        # Update moments
        self.m = [
            self.beta1 * m + (1 - self.beta1) * g
            for m, g in zip(self.m, gradient)
        ]
        self.v = [
            self.beta2 * v + (1 - self.beta2) * g ** 2
            for v, g in zip(self.v, gradient)
        ]
        
        # Bias correction
        m_hat = [m / (1 - self.beta1 ** self.t) for m in self.m]
        v_hat = [v / (1 - self.beta2 ** self.t) for v in self.v]
        
        # Update with decoupled weight decay
        weights = [
            w * (1 - self.lr * self.weight_decay) - 
            self.lr * m_h / (math.sqrt(v_h) + self.epsilon)
            for w, m_h, v_h in zip(weights, m_hat, v_hat)
        ]
        
        return weights


class LearningRateScheduler:
    """
    Learning rate scheduling utilities.
    """
    
    @staticmethod
    def step_decay(
        initial_lr: float,
        epoch: int,
        drop_rate: float = 0.5,
        epochs_drop: int = 10
    ) -> float:
        """
        Step decay: reduce LR by factor every N epochs.
        
        >>> LearningRateScheduler.step_decay(0.1, 0)
        0.1
        >>> LearningRateScheduler.step_decay(0.1, 10)
        0.05
        """
        return initial_lr * (drop_rate ** (epoch // epochs_drop))
    
    @staticmethod
    def exponential_decay(
        initial_lr: float,
        epoch: int,
        decay_rate: float = 0.95
    ) -> float:
        """
        Exponential decay: LR *= decay_rate each epoch.
        
        >>> abs(LearningRateScheduler.exponential_decay(0.1, 10, 0.9) - 0.03486) < 0.001
        True
        """
        return initial_lr * (decay_rate ** epoch)
    
    @staticmethod
    def cosine_annealing(
        initial_lr: float,
        epoch: int,
        total_epochs: int,
        min_lr: float = 0.0
    ) -> float:
        """
        Cosine annealing schedule.
        
        >>> LearningRateScheduler.cosine_annealing(0.1, 0, 100)
        0.1
        """
        return min_lr + 0.5 * (initial_lr - min_lr) * (
            1 + math.cos(math.pi * epoch / total_epochs)
        )
    
    @staticmethod
    def warmup_linear(
        initial_lr: float,
        epoch: int,
        warmup_epochs: int,
        total_epochs: int
    ) -> float:
        """
        Linear warmup followed by linear decay.
        """
        if epoch < warmup_epochs:
            return initial_lr * epoch / warmup_epochs
        else:
            return initial_lr * (total_epochs - epoch) / (total_epochs - warmup_epochs)


class MiniBatchTrainer:
    """
    Mini-batch training framework.
    """
    
    def __init__(
        self,
        optimizer: any,
        batch_size: int = 32
    ):
        self.optimizer = optimizer
        self.batch_size = batch_size
    
    def train_epoch(
        self,
        X: List[List[float]],
        y: List[float],
        weights: List[float],
        loss_grad_func: Callable
    ) -> Tuple[List[float], float]:
        """
        Train for one epoch.
        
        Args:
            X: Training features
            y: Training targets
            weights: Current weights
            loss_grad_func: Function returning (loss, gradient)
        
        Returns:
            Updated weights and average loss.
        """
        n = len(X)
        indices = list(range(n))
        random.shuffle(indices)
        
        total_loss = 0.0
        n_batches = 0
        
        for i in range(0, n, self.batch_size):
            batch_indices = indices[i:i + self.batch_size]
            
            X_batch = [X[j] for j in batch_indices]
            y_batch = [y[j] for j in batch_indices]
            
            loss, gradient = loss_grad_func(weights, X_batch, y_batch)
            
            weights = self.optimizer.step(weights, gradient)
            
            total_loss += loss
            n_batches += 1
        
        return weights, total_loss / n_batches
    
    def train(
        self,
        X: List[List[float]],
        y: List[float],
        initial_weights: List[float],
        loss_grad_func: Callable,
        epochs: int = 100,
        verbose: bool = False
    ) -> Tuple[List[float], List[float]]:
        """
        Full training loop.
        
        Returns final weights and loss history.
        """
        weights = initial_weights.copy()
        losses = []
        
        for epoch in range(epochs):
            weights, avg_loss = self.train_epoch(X, y, weights, loss_grad_func)
            losses.append(avg_loss)
            
            if verbose and (epoch + 1) % 10 == 0:
                print(f"Epoch {epoch + 1}: Loss = {avg_loss:.6f}")
        
        return weights, losses
```

## 8. Applications

### 8.1 Common Use Cases

- Neural network training
- Linear/logistic regression
- Matrix factorization
- Reinforcement learning
- Any differentiable optimization

### 8.2 Optimizer Selection Guide

| Scenario | Recommended |
|----------|-------------|
| General purpose | Adam |
| Sparse gradients | AdaGrad |
| Convex problems | SGD + Momentum |
| Large models | AdamW |
| Fine-tuning | SGD (low LR) |

## 9. Real-World Software Engineering Applications

### 9.1 Production Example: Neural Network Training

```python
from typing import List, Tuple, Callable, Optional
import math
import random


class NeuralNetworkTrainer:
    """
    Production neural network trainer with gradient descent.
    """
    
    def __init__(
        self,
        optimizer: str = 'adam',
        learning_rate: float = 0.001,
        batch_size: int = 32,
        epochs: int = 100
    ):
        """
        Initialize trainer.
        
        Args:
            optimizer: 'sgd', 'momentum', 'adam', or 'rmsprop'
            learning_rate: Initial learning rate
            batch_size: Mini-batch size
            epochs: Number of training epochs
        """
        self.optimizer_name = optimizer
        self.lr = learning_rate
        self.batch_size = batch_size
        self.epochs = epochs
        
        self.optimizer = self._create_optimizer()
        self.scheduler = None
        
        self.history = {
            'train_loss': [],
            'val_loss': [],
            'learning_rates': []
        }
    
    def _create_optimizer(self):
        """Create optimizer instance."""
        if self.optimizer_name == 'sgd':
            return SGD(learning_rate=self.lr)
        elif self.optimizer_name == 'momentum':
            return SGD(learning_rate=self.lr, momentum=0.9)
        elif self.optimizer_name == 'adam':
            return Adam(learning_rate=self.lr)
        elif self.optimizer_name == 'rmsprop':
            return RMSprop(learning_rate=self.lr)
        else:
            raise ValueError(f"Unknown optimizer: {self.optimizer_name}")
    
    def set_scheduler(
        self,
        scheduler_type: str,
        **kwargs
    ):
        """Set learning rate scheduler."""
        if scheduler_type == 'step':
            self.scheduler = lambda epoch: LearningRateScheduler.step_decay(
                self.lr, epoch, **kwargs
            )
        elif scheduler_type == 'cosine':
            self.scheduler = lambda epoch: LearningRateScheduler.cosine_annealing(
                self.lr, epoch, self.epochs, **kwargs
            )
        elif scheduler_type == 'exponential':
            self.scheduler = lambda epoch: LearningRateScheduler.exponential_decay(
                self.lr, epoch, **kwargs
            )
    
    def _update_learning_rate(self, epoch: int):
        """Update optimizer learning rate."""
        if self.scheduler is not None:
            new_lr = self.scheduler(epoch)
            self.optimizer.lr = new_lr
            return new_lr
        return self.lr
    
    def fit(
        self,
        model_forward: Callable,
        model_backward: Callable,
        get_weights: Callable,
        set_weights: Callable,
        X_train: List[List[float]],
        y_train: List[float],
        X_val: Optional[List[List[float]]] = None,
        y_val: Optional[List[float]] = None,
        early_stopping_patience: int = 10,
        verbose: bool = True
    ) -> dict:
        """
        Train model with gradient descent.
        
        Args:
            model_forward: Forward pass function
            model_backward: Backward pass returning gradients
            get_weights: Function to get model weights
            set_weights: Function to set model weights
            X_train, y_train: Training data
            X_val, y_val: Validation data (optional)
            early_stopping_patience: Epochs to wait for improvement
            verbose: Print progress
        
        Returns:
            Training history.
        """
        n_train = len(X_train)
        best_val_loss = float('inf')
        patience_counter = 0
        best_weights = None
        
        for epoch in range(self.epochs):
            # Update learning rate
            current_lr = self._update_learning_rate(epoch)
            self.history['learning_rates'].append(current_lr)
            
            # Shuffle training data
            indices = list(range(n_train))
            random.shuffle(indices)
            
            epoch_loss = 0.0
            n_batches = 0
            
            # Mini-batch training
            for i in range(0, n_train, self.batch_size):
                batch_indices = indices[i:i + self.batch_size]
                
                X_batch = [X_train[j] for j in batch_indices]
                y_batch = [y_train[j] for j in batch_indices]
                
                # Forward pass
                predictions, loss = model_forward(X_batch, y_batch)
                
                # Backward pass
                gradients = model_backward(X_batch, y_batch, predictions)
                
                # Update weights
                weights = get_weights()
                new_weights = self.optimizer.step(weights, gradients)
                set_weights(new_weights)
                
                epoch_loss += loss
                n_batches += 1
            
            avg_train_loss = epoch_loss / n_batches
            self.history['train_loss'].append(avg_train_loss)
            
            # Validation
            if X_val is not None and y_val is not None:
                _, val_loss = model_forward(X_val, y_val)
                self.history['val_loss'].append(val_loss)
                
                # Early stopping
                if val_loss < best_val_loss:
                    best_val_loss = val_loss
                    best_weights = get_weights().copy()
                    patience_counter = 0
                else:
                    patience_counter += 1
                    
                    if patience_counter >= early_stopping_patience:
                        if verbose:
                            print(f"Early stopping at epoch {epoch + 1}")
                        break
            
            # Logging
            if verbose and (epoch + 1) % 10 == 0:
                msg = f"Epoch {epoch + 1}/{self.epochs} - Loss: {avg_train_loss:.6f}"
                if X_val is not None:
                    msg += f" - Val Loss: {self.history['val_loss'][-1]:.6f}"
                msg += f" - LR: {current_lr:.6f}"
                print(msg)
        
        # Restore best weights
        if best_weights is not None:
            set_weights(best_weights)
        
        return self.history


class HyperparameterTuner:
    """
    Hyperparameter tuning for gradient descent.
    """
    
    def __init__(self):
        self.results = []
    
    def grid_search(
        self,
        train_func: Callable,
        param_grid: dict,
        X: List[List[float]],
        y: List[float],
        n_folds: int = 3
    ) -> dict:
        """
        Grid search over hyperparameters.
        
        Args:
            train_func: Function(params, X, y) -> validation_loss
            param_grid: Dictionary of parameter lists
            X, y: Training data
            n_folds: Cross-validation folds
        
        Returns:
            Best parameters and results.
        """
        from itertools import product
        
        param_names = list(param_grid.keys())
        param_values = list(param_grid.values())
        
        best_params = None
        best_score = float('inf')
        
        for values in product(*param_values):
            params = dict(zip(param_names, values))
            
            # Cross-validation
            fold_size = len(X) // n_folds
            fold_scores = []
            
            for fold in range(n_folds):
                val_start = fold * fold_size
                val_end = (fold + 1) * fold_size
                
                X_train = X[:val_start] + X[val_end:]
                y_train = y[:val_start] + y[val_end:]
                X_val = X[val_start:val_end]
                y_val = y[val_start:val_end]
                
                score = train_func(params, X_train, y_train, X_val, y_val)
                fold_scores.append(score)
            
            avg_score = sum(fold_scores) / len(fold_scores)
            
            self.results.append({
                'params': params,
                'avg_score': avg_score,
                'fold_scores': fold_scores
            })
            
            if avg_score < best_score:
                best_score = avg_score
                best_params = params
        
        return {
            'best_params': best_params,
            'best_score': best_score,
            'all_results': self.results
        }
    
    def random_search(
        self,
        train_func: Callable,
        param_distributions: dict,
        X: List[List[float]],
        y: List[float],
        n_iter: int = 20,
        n_folds: int = 3
    ) -> dict:
        """
        Random search over hyperparameters.
        
        Args:
            train_func: Training function
            param_distributions: Dict of (min, max) tuples or lists
            X, y: Training data
            n_iter: Number of random combinations
            n_folds: Cross-validation folds
        
        Returns:
            Best parameters and results.
        """
        best_params = None
        best_score = float('inf')
        
        for _ in range(n_iter):
            params = {}
            for name, dist in param_distributions.items():
                if isinstance(dist, tuple) and len(dist) == 2:
                    # Continuous: (min, max)
                    if isinstance(dist[0], float):
                        # Log scale for learning rate
                        if 'lr' in name or 'learning_rate' in name:
                            params[name] = 10 ** random.uniform(
                                math.log10(dist[0]), math.log10(dist[1])
                            )
                        else:
                            params[name] = random.uniform(dist[0], dist[1])
                    else:
                        params[name] = random.randint(dist[0], dist[1])
                else:
                    # Categorical
                    params[name] = random.choice(dist)
            
            # Evaluate with cross-validation
            fold_size = len(X) // n_folds
            fold_scores = []
            
            for fold in range(n_folds):
                val_start = fold * fold_size
                val_end = (fold + 1) * fold_size
                
                X_train = X[:val_start] + X[val_end:]
                y_train = y[:val_start] + y[val_end:]
                X_val = X[val_start:val_end]
                y_val = y[val_start:val_end]
                
                score = train_func(params, X_train, y_train, X_val, y_val)
                fold_scores.append(score)
            
            avg_score = sum(fold_scores) / len(fold_scores)
            
            self.results.append({
                'params': params,
                'avg_score': avg_score
            })
            
            if avg_score < best_score:
                best_score = avg_score
                best_params = params
        
        return {
            'best_params': best_params,
            'best_score': best_score,
            'all_results': self.results
        }


# Demo
if __name__ == "__main__":
    print("Gradient Descent Optimizers Demo")
    print("=" * 50)
    
    # Simple quadratic function: f(w) = sum(w^2)
    def quadratic_gradient(w):
        return [2 * wi for wi in w]
    
    initial = [5.0, -3.0, 2.0]
    
    print("Optimizing f(w) = ||w||^2, starting from", initial)
    print()
    
    # Test different optimizers
    optimizers = [
        ("SGD", SGD(learning_rate=0.1)),
        ("SGD + Momentum", SGD(learning_rate=0.1, momentum=0.9)),
        ("AdaGrad", AdaGrad(learning_rate=0.5)),
        ("RMSprop", RMSprop(learning_rate=0.1)),
        ("Adam", Adam(learning_rate=0.1))
    ]
    
    for name, opt in optimizers:
        w = initial.copy()
        
        for _ in range(100):
            grad = quadratic_gradient(w)
            w = opt.step(w, grad)
        
        final_norm = math.sqrt(sum(wi ** 2 for wi in w))
        print(f"{name:20s}: Final ||w|| = {final_norm:.6f}")
    
    print("\n" + "=" * 50)
    print("Learning Rate Scheduling")
    print("=" * 50)
    
    schedules = [
        ("Step decay (drop every 10)", lambda e: LearningRateScheduler.step_decay(0.1, e)),
        ("Exponential (0.95)", lambda e: LearningRateScheduler.exponential_decay(0.1, e, 0.95)),
        ("Cosine annealing", lambda e: LearningRateScheduler.cosine_annealing(0.1, e, 50))
    ]
    
    for name, sched in schedules:
        print(f"\n{name}:")
        for epoch in [0, 10, 25, 40, 50]:
            lr = sched(epoch)
            print(f"  Epoch {epoch:2d}: LR = {lr:.4f}")
```

## 10. Comparison of Optimizers

| Optimizer | Pros | Cons | Best For |
|-----------|------|------|----------|
| SGD | Simple, generalizes well | Slow, sensitive to LR | Convex problems |
| Momentum | Faster convergence | Extra hyperparameter | Most problems |
| AdaGrad | Good for sparse | LR decays too fast | NLP, sparse features |
| RMSprop | Addresses AdaGrad | Less stable | Non-stationary |
| Adam | Fast, adaptive | Memory, generalization | Default choice |

## 11. Best Practices

1. **Start with Adam** for most problems
2. **Use learning rate scheduling** for better convergence
3. **Monitor gradients** for vanishing/exploding
4. **Batch normalization** stabilizes training
5. **Gradient clipping** for stability
6. **Warm up** learning rate for large batches

## 12. References

- [Wikipedia: Gradient Descent](https://en.wikipedia.org/wiki/Gradient_descent)
- Kingma, D.P. and Ba, J. "Adam: A Method for Stochastic Optimization" (2014)
- Ruder, S. "An Overview of Gradient Descent Optimization Algorithms" (2016)
