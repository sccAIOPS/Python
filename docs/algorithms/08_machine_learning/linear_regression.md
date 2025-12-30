# Linear Regression

## Overview
- **Category**: Supervised Learning - Regression
- **Complexity**: Training: O(nd² + d³) | Prediction: O(d)
- **Type**: Parametric model
- **Source File**: [machine_learning/linear_regression.py](../../../machine_learning/linear_regression.py)

## 1. Mathematical Foundation

### 1.1 The Problem

Given data points $(x_1, y_1), \ldots, (x_n, y_n)$ where $x_i \in \mathbb{R}^d$, find the best linear relationship:

$$
\hat{y} = w_0 + w_1 x_1 + w_2 x_2 + \ldots + w_d x_d = \mathbf{w}^T \mathbf{x}
$$

### 1.2 Matrix Formulation

**Design matrix** $\mathbf{X} \in \mathbb{R}^{n \times (d+1)}$ (with bias column of 1s):
$$
\mathbf{X} = \begin{bmatrix} 1 & x_{11} & \cdots & x_{1d} \\ \vdots & \vdots & \ddots & \vdots \\ 1 & x_{n1} & \cdots & x_{nd} \end{bmatrix}
$$

**Model**: $\hat{\mathbf{y}} = \mathbf{X}\mathbf{w}$

### 1.3 Loss Function

**Mean Squared Error (MSE)**:
$$
L(\mathbf{w}) = \frac{1}{n} \sum_{i=1}^{n} (y_i - \hat{y}_i)^2 = \frac{1}{n} \|\mathbf{y} - \mathbf{X}\mathbf{w}\|^2
$$

### 1.4 Normal Equation (Closed-Form Solution)

Setting $\nabla L = 0$:
$$
\mathbf{w}^* = (\mathbf{X}^T \mathbf{X})^{-1} \mathbf{X}^T \mathbf{y}
$$

The matrix $(\mathbf{X}^T \mathbf{X})^{-1} \mathbf{X}^T$ is called the **pseudo-inverse** of $\mathbf{X}$.

### 1.5 Geometric Interpretation

The solution projects $\mathbf{y}$ onto the column space of $\mathbf{X}$:
$$
\hat{\mathbf{y}} = \mathbf{X}(\mathbf{X}^T \mathbf{X})^{-1} \mathbf{X}^T \mathbf{y}
$$

The projection matrix $\mathbf{H} = \mathbf{X}(\mathbf{X}^T \mathbf{X})^{-1} \mathbf{X}^T$ is called the **hat matrix**.

## 2. Gradient Descent Alternative

### 2.1 Gradient Computation

$$
\nabla L = -\frac{2}{n} \mathbf{X}^T (\mathbf{y} - \mathbf{X}\mathbf{w})
$$

### 2.2 Update Rule

$$
\mathbf{w}_{t+1} = \mathbf{w}_t - \alpha \nabla L = \mathbf{w}_t + \frac{2\alpha}{n} \mathbf{X}^T (\mathbf{y} - \mathbf{X}\mathbf{w}_t)
$$

### 2.3 Stochastic Gradient Descent

Update using single sample $(x_i, y_i)$:
$$
\mathbf{w}_{t+1} = \mathbf{w}_t + \alpha (y_i - \mathbf{w}_t^T x_i) x_i
$$

## 3. Regularization

### 3.1 Ridge Regression (L2)

$$
L(\mathbf{w}) = \|\mathbf{y} - \mathbf{X}\mathbf{w}\|^2 + \lambda \|\mathbf{w}\|^2
$$

**Solution**:
$$
\mathbf{w}^* = (\mathbf{X}^T \mathbf{X} + \lambda \mathbf{I})^{-1} \mathbf{X}^T \mathbf{y}
$$

### 3.2 Lasso Regression (L1)

$$
L(\mathbf{w}) = \|\mathbf{y} - \mathbf{X}\mathbf{w}\|^2 + \lambda \|\mathbf{w}\|_1
$$

No closed-form solution; requires iterative methods (coordinate descent, proximal gradient).

### 3.3 Elastic Net

$$
L(\mathbf{w}) = \|\mathbf{y} - \mathbf{X}\mathbf{w}\|^2 + \lambda_1 \|\mathbf{w}\|_1 + \lambda_2 \|\mathbf{w}\|^2
$$

## 4. Pseudocode

```
ALGORITHM LinearRegression-NormalEquation(X, y)
    INPUT: Design matrix X (n×d), target vector y (n×1)
    OUTPUT: Weight vector w
    
    // Add bias column
    X ← [ones(n,1), X]
    
    // Compute normal equation
    XtX ← X^T × X
    Xty ← X^T × y
    w ← solve(XtX, Xty)  // or XtX^{-1} × Xty
    
    return w

ALGORITHM LinearRegression-GradientDescent(X, y, α, max_iter)
    INPUT: X, y, learning rate α, max iterations
    OUTPUT: Weight vector w
    
    n, d ← shape(X)
    X ← [ones(n,1), X]
    w ← zeros(d+1)
    
    for iter ← 1 to max_iter do
        predictions ← X × w
        errors ← y - predictions
        gradient ← -(2/n) × X^T × errors
        w ← w - α × gradient
        
        // Optional: check convergence
        if ||gradient|| < tolerance then
            break
    
    return w

ALGORITHM LinearRegression-SGD(X, y, α, epochs)
    INPUT: X, y, learning rate α, number of epochs
    OUTPUT: Weight vector w
    
    n, d ← shape(X)
    X ← [ones(n,1), X]
    w ← zeros(d+1)
    
    for epoch ← 1 to epochs do
        shuffle indices
        for i in shuffled indices do
            prediction ← dot(w, X[i])
            error ← y[i] - prediction
            w ← w + α × error × X[i]
    
    return w
```

## 5. Step-by-Step Example

### Example: Fit line to points (1,2), (2,4), (3,5), (4,4), (5,5)

**Design matrix (with bias)**:
$$
\mathbf{X} = \begin{bmatrix} 1 & 1 \\ 1 & 2 \\ 1 & 3 \\ 1 & 4 \\ 1 & 5 \end{bmatrix}, \quad
\mathbf{y} = \begin{bmatrix} 2 \\ 4 \\ 5 \\ 4 \\ 5 \end{bmatrix}
$$

**Compute**:
$$
\mathbf{X}^T\mathbf{X} = \begin{bmatrix} 5 & 15 \\ 15 & 55 \end{bmatrix}, \quad
\mathbf{X}^T\mathbf{y} = \begin{bmatrix} 20 \\ 67 \end{bmatrix}
$$

**Solve**:
$$
\mathbf{w} = \begin{bmatrix} 5 & 15 \\ 15 & 55 \end{bmatrix}^{-1} \begin{bmatrix} 20 \\ 67 \end{bmatrix} = \begin{bmatrix} 2.2 \\ 0.6 \end{bmatrix}
$$

**Result**: $\hat{y} = 2.2 + 0.6x$

## 6. Complexity Analysis

| Method | Time | Space | Notes |
|--------|------|-------|-------|
| Normal Equation | O(nd² + d³) | O(d²) | Best for d < 10,000 |
| Gradient Descent | O(ndk) | O(d) | k = iterations |
| SGD | O(ndk) | O(d) | Faster convergence |

### 6.1 When to Use Which

| Scenario | Recommended Method |
|----------|-------------------|
| Small d (< 10,000) | Normal equation |
| Large d or sparse | Gradient descent |
| Online learning | SGD |
| Ill-conditioned X^TX | Ridge regression |

## 7. Visual Representation

### 7.1 2D Linear Regression

```
    y │
      │         ●
    5 │       ●/
      │     ●/
    4 │   ●/
      │  /●
    3 │ /
      │/
    2 │●
      │
    1 │
      └──────────────── x
        1  2  3  4  5
        
Best fit line: ŷ = 2.2 + 0.6x
```

### 7.2 Residuals

```
    y │
      │    ●─┐  residual
      │    │ │
      │    ●←┘
      │   /│
      │  / │
      │ /  ●
      │/   │
      ●────┘
      └──────────────── x

Residual = y_actual - y_predicted
```

### 7.3 Gradient Descent Convergence

```
Loss │
     │\
     │ \
     │  \
     │   \
     │    \_____
     └────────────── Iterations
```

## 8. Implementation

```python
from typing import Tuple, Optional, List
import math


class LinearRegression:
    """
    Linear regression implementation from scratch.
    
    Supports both closed-form and gradient descent solutions.
    """
    
    def __init__(
        self,
        method: str = 'normal',
        learning_rate: float = 0.01,
        max_iterations: int = 1000,
        tolerance: float = 1e-6,
        regularization: Optional[str] = None,
        lambda_param: float = 0.1
    ):
        """
        Initialize linear regression model.
        
        Args:
            method: 'normal', 'gd' (gradient descent), or 'sgd'
            learning_rate: Step size for gradient descent
            max_iterations: Maximum iterations for GD
            tolerance: Convergence threshold
            regularization: None, 'l2' (ridge), or 'l1' (lasso)
            lambda_param: Regularization strength
        """
        self.method = method
        self.lr = learning_rate
        self.max_iter = max_iterations
        self.tol = tolerance
        self.reg = regularization
        self.lambda_ = lambda_param
        
        self.weights: Optional[List[float]] = None
        self.history: List[float] = []
    
    def _add_bias(self, X: List[List[float]]) -> List[List[float]]:
        """Add column of 1s for bias term."""
        return [[1.0] + row for row in X]
    
    def _dot(self, v1: List[float], v2: List[float]) -> float:
        """Dot product of two vectors."""
        return sum(a * b for a, b in zip(v1, v2))
    
    def _matrix_multiply(
        self, 
        A: List[List[float]], 
        B: List[List[float]]
    ) -> List[List[float]]:
        """Multiply two matrices."""
        n, m = len(A), len(A[0])
        m2, p = len(B), len(B[0])
        
        result = [[0.0] * p for _ in range(n)]
        for i in range(n):
            for j in range(p):
                for k in range(m):
                    result[i][j] += A[i][k] * B[k][j]
        return result
    
    def _transpose(self, A: List[List[float]]) -> List[List[float]]:
        """Transpose matrix."""
        return [[A[j][i] for j in range(len(A))] for i in range(len(A[0]))]
    
    def _matrix_vector_multiply(
        self, 
        A: List[List[float]], 
        v: List[float]
    ) -> List[float]:
        """Multiply matrix by vector."""
        return [self._dot(row, v) for row in A]
    
    def _solve_linear_system(
        self, 
        A: List[List[float]], 
        b: List[float]
    ) -> List[float]:
        """
        Solve Ax = b using Gaussian elimination with partial pivoting.
        """
        n = len(A)
        
        # Augmented matrix
        aug = [A[i][:] + [b[i]] for i in range(n)]
        
        # Forward elimination
        for col in range(n):
            # Find pivot
            max_row = col
            for row in range(col + 1, n):
                if abs(aug[row][col]) > abs(aug[max_row][col]):
                    max_row = row
            aug[col], aug[max_row] = aug[max_row], aug[col]
            
            if abs(aug[col][col]) < 1e-10:
                raise ValueError("Matrix is singular")
            
            # Eliminate
            for row in range(col + 1, n):
                factor = aug[row][col] / aug[col][col]
                for j in range(col, n + 1):
                    aug[row][j] -= factor * aug[col][j]
        
        # Back substitution
        x = [0.0] * n
        for i in range(n - 1, -1, -1):
            x[i] = aug[i][n]
            for j in range(i + 1, n):
                x[i] -= aug[i][j] * x[j]
            x[i] /= aug[i][i]
        
        return x
    
    def _compute_mse(
        self, 
        X: List[List[float]], 
        y: List[float]
    ) -> float:
        """Compute mean squared error."""
        n = len(y)
        predictions = self._matrix_vector_multiply(X, self.weights)
        mse = sum((y[i] - predictions[i]) ** 2 for i in range(n)) / n
        return mse
    
    def fit_normal_equation(
        self, 
        X: List[List[float]], 
        y: List[float]
    ) -> 'LinearRegression':
        """
        Fit using normal equation: w = (X^T X)^{-1} X^T y
        
        >>> lr = LinearRegression()
        >>> X = [[1], [2], [3], [4], [5]]
        >>> y = [2, 4, 5, 4, 5]
        >>> lr.fit_normal_equation(X, y)  # doctest: +ELLIPSIS
        <...LinearRegression object at ...>
        >>> abs(lr.weights[0] - 2.2) < 0.1
        True
        """
        X_bias = self._add_bias(X)
        n = len(X_bias)
        d = len(X_bias[0])
        
        # Compute X^T X
        X_T = self._transpose(X_bias)
        XTX = self._matrix_multiply(X_T, X_bias)
        
        # Add regularization if ridge
        if self.reg == 'l2':
            for i in range(1, d):  # Don't regularize bias
                XTX[i][i] += self.lambda_
        
        # Compute X^T y
        XTy = self._matrix_vector_multiply(X_T, y)
        
        # Solve
        self.weights = self._solve_linear_system(XTX, XTy)
        
        return self
    
    def fit_gradient_descent(
        self, 
        X: List[List[float]], 
        y: List[float]
    ) -> 'LinearRegression':
        """
        Fit using batch gradient descent.
        
        >>> lr = LinearRegression(method='gd', learning_rate=0.1, max_iterations=1000)
        >>> X = [[1], [2], [3], [4], [5]]
        >>> y = [2, 4, 5, 4, 5]
        >>> lr.fit_gradient_descent(X, y)  # doctest: +ELLIPSIS
        <...LinearRegression object at ...>
        >>> abs(lr.weights[0] - 2.2) < 0.3
        True
        """
        X_bias = self._add_bias(X)
        n = len(X_bias)
        d = len(X_bias[0])
        
        # Initialize weights
        self.weights = [0.0] * d
        self.history = []
        
        X_T = self._transpose(X_bias)
        
        for iteration in range(self.max_iter):
            # Predictions
            predictions = self._matrix_vector_multiply(X_bias, self.weights)
            
            # Errors
            errors = [y[i] - predictions[i] for i in range(n)]
            
            # Gradient: -2/n * X^T * errors
            gradient = self._matrix_vector_multiply(X_T, errors)
            gradient = [-2 * g / n for g in gradient]
            
            # Add regularization gradient
            if self.reg == 'l2':
                for i in range(1, d):
                    gradient[i] += 2 * self.lambda_ * self.weights[i]
            
            # Update
            self.weights = [
                self.weights[i] - self.lr * gradient[i] 
                for i in range(d)
            ]
            
            # Track loss
            mse = self._compute_mse(X_bias, y)
            self.history.append(mse)
            
            # Check convergence
            if math.sqrt(sum(g**2 for g in gradient)) < self.tol:
                break
        
        return self
    
    def fit_sgd(
        self, 
        X: List[List[float]], 
        y: List[float],
        epochs: int = 100
    ) -> 'LinearRegression':
        """
        Fit using stochastic gradient descent.
        """
        import random
        
        X_bias = self._add_bias(X)
        n = len(X_bias)
        d = len(X_bias[0])
        
        self.weights = [0.0] * d
        self.history = []
        
        for epoch in range(epochs):
            indices = list(range(n))
            random.shuffle(indices)
            
            for i in indices:
                prediction = self._dot(self.weights, X_bias[i])
                error = y[i] - prediction
                
                for j in range(d):
                    update = self.lr * error * X_bias[i][j]
                    if self.reg == 'l2' and j > 0:
                        update -= self.lr * self.lambda_ * self.weights[j]
                    self.weights[j] += update
            
            mse = self._compute_mse(X_bias, y)
            self.history.append(mse)
        
        return self
    
    def fit(
        self, 
        X: List[List[float]], 
        y: List[float]
    ) -> 'LinearRegression':
        """
        Fit the model using the configured method.
        """
        if self.method == 'normal':
            return self.fit_normal_equation(X, y)
        elif self.method == 'gd':
            return self.fit_gradient_descent(X, y)
        elif self.method == 'sgd':
            return self.fit_sgd(X, y)
        else:
            raise ValueError(f"Unknown method: {self.method}")
    
    def predict(self, X: List[List[float]]) -> List[float]:
        """
        Make predictions on new data.
        
        >>> lr = LinearRegression()
        >>> lr.fit([[1], [2], [3]], [2, 4, 6])  # doctest: +ELLIPSIS
        <...>
        >>> predictions = lr.predict([[4], [5]])
        >>> all(7 < p < 11 for p in predictions)
        True
        """
        if self.weights is None:
            raise ValueError("Model not fitted")
        
        X_bias = self._add_bias(X)
        return self._matrix_vector_multiply(X_bias, self.weights)
    
    def score(self, X: List[List[float]], y: List[float]) -> float:
        """
        Compute R² score.
        
        R² = 1 - SS_res / SS_tot
        """
        predictions = self.predict(X)
        y_mean = sum(y) / len(y)
        
        ss_res = sum((y[i] - predictions[i])**2 for i in range(len(y)))
        ss_tot = sum((y[i] - y_mean)**2 for i in range(len(y)))
        
        if ss_tot == 0:
            return 1.0 if ss_res == 0 else 0.0
        
        return 1 - ss_res / ss_tot
    
    @property
    def coefficients(self) -> List[float]:
        """Return feature coefficients (excluding bias)."""
        if self.weights is None:
            return []
        return self.weights[1:]
    
    @property
    def intercept(self) -> float:
        """Return intercept (bias) term."""
        if self.weights is None:
            return 0.0
        return self.weights[0]


def polynomial_features(
    X: List[List[float]], 
    degree: int
) -> List[List[float]]:
    """
    Generate polynomial features up to given degree.
    
    >>> polynomial_features([[2], [3]], 2)
    [[2, 4], [3, 9]]
    """
    result = []
    for row in X:
        new_row = []
        for x in row:
            for d in range(1, degree + 1):
                new_row.append(x ** d)
        result.append(new_row)
    return result


class PolynomialRegression:
    """
    Polynomial regression using linear regression with polynomial features.
    """
    
    def __init__(self, degree: int = 2, **kwargs):
        self.degree = degree
        self.lr = LinearRegression(**kwargs)
    
    def fit(
        self, 
        X: List[List[float]], 
        y: List[float]
    ) -> 'PolynomialRegression':
        X_poly = polynomial_features(X, self.degree)
        self.lr.fit(X_poly, y)
        return self
    
    def predict(self, X: List[List[float]]) -> List[float]:
        X_poly = polynomial_features(X, self.degree)
        return self.lr.predict(X_poly)
    
    def score(self, X: List[List[float]], y: List[float]) -> float:
        X_poly = polynomial_features(X, self.degree)
        return self.lr.score(X_poly, y)
```

## 9. Applications

### 9.1 Classic Applications

- House price prediction
- Stock price forecasting
- Sales prediction
- Demand forecasting
- Scientific curve fitting

### 9.2 Feature Engineering

- Polynomial features for nonlinear relationships
- Interaction terms
- Log/exponential transforms
- Normalization/standardization

## 10. Real-World Software Engineering Applications

### 10.1 Industry Use Cases

1. **E-commerce**
   - Price optimization
   - Demand forecasting
   - Customer lifetime value

2. **Finance**
   - Risk modeling (CAPM: $r = \alpha + \beta r_m$)
   - Credit scoring components
   - Time series trends

3. **Operations**
   - Capacity planning
   - Cost estimation
   - Performance modeling

### 10.2 Production Example: ML Pipeline

```python
from typing import List, Tuple, Dict
import random
import math


class DataPreprocessor:
    """Standardize features for linear regression."""
    
    def __init__(self):
        self.means: List[float] = []
        self.stds: List[float] = []
    
    def fit(self, X: List[List[float]]) -> 'DataPreprocessor':
        n = len(X)
        d = len(X[0])
        
        self.means = []
        self.stds = []
        
        for j in range(d):
            col = [X[i][j] for i in range(n)]
            mean = sum(col) / n
            std = math.sqrt(sum((x - mean)**2 for x in col) / n)
            self.means.append(mean)
            self.stds.append(std if std > 1e-10 else 1.0)
        
        return self
    
    def transform(self, X: List[List[float]]) -> List[List[float]]:
        return [
            [(X[i][j] - self.means[j]) / self.stds[j] 
             for j in range(len(X[0]))]
            for i in range(len(X))
        ]
    
    def fit_transform(self, X: List[List[float]]) -> List[List[float]]:
        self.fit(X)
        return self.transform(X)


class RegressionPipeline:
    """
    Complete regression pipeline with preprocessing and evaluation.
    """
    
    def __init__(self, regularization: str = 'l2', lambda_param: float = 0.01):
        self.preprocessor = DataPreprocessor()
        self.model = LinearRegression(
            method='gd',
            regularization=regularization,
            lambda_param=lambda_param,
            max_iterations=5000
        )
    
    def train_test_split(
        self,
        X: List[List[float]],
        y: List[float],
        test_size: float = 0.2,
        seed: int = 42
    ) -> Tuple[List[List[float]], List[List[float]], List[float], List[float]]:
        """Split data into train and test sets."""
        random.seed(seed)
        
        n = len(X)
        indices = list(range(n))
        random.shuffle(indices)
        
        split = int(n * (1 - test_size))
        
        train_idx = indices[:split]
        test_idx = indices[split:]
        
        X_train = [X[i] for i in train_idx]
        X_test = [X[i] for i in test_idx]
        y_train = [y[i] for i in train_idx]
        y_test = [y[i] for i in test_idx]
        
        return X_train, X_test, y_train, y_test
    
    def cross_validate(
        self,
        X: List[List[float]],
        y: List[float],
        k: int = 5
    ) -> Dict[str, float]:
        """K-fold cross-validation."""
        n = len(X)
        fold_size = n // k
        
        scores = []
        
        for fold in range(k):
            # Create fold indices
            test_start = fold * fold_size
            test_end = test_start + fold_size
            
            X_train = X[:test_start] + X[test_end:]
            y_train = y[:test_start] + y[test_end:]
            X_test = X[test_start:test_end]
            y_test = y[test_start:test_end]
            
            # Fit and evaluate
            X_train_scaled = self.preprocessor.fit_transform(X_train)
            X_test_scaled = self.preprocessor.transform(X_test)
            
            self.model.fit(X_train_scaled, y_train)
            score = self.model.score(X_test_scaled, y_test)
            scores.append(score)
        
        return {
            'mean_r2': sum(scores) / len(scores),
            'std_r2': math.sqrt(sum((s - sum(scores)/len(scores))**2 
                                   for s in scores) / len(scores)),
            'scores': scores
        }
    
    def fit(self, X: List[List[float]], y: List[float]) -> 'RegressionPipeline':
        """Fit the complete pipeline."""
        X_scaled = self.preprocessor.fit_transform(X)
        self.model.fit(X_scaled, y)
        return self
    
    def predict(self, X: List[List[float]]) -> List[float]:
        """Make predictions."""
        X_scaled = self.preprocessor.transform(X)
        return self.model.predict(X_scaled)
    
    def evaluate(
        self, 
        X: List[List[float]], 
        y: List[float]
    ) -> Dict[str, float]:
        """Compute evaluation metrics."""
        predictions = self.predict(X)
        n = len(y)
        
        # MSE
        mse = sum((y[i] - predictions[i])**2 for i in range(n)) / n
        
        # RMSE
        rmse = math.sqrt(mse)
        
        # MAE
        mae = sum(abs(y[i] - predictions[i]) for i in range(n)) / n
        
        # R²
        r2 = self.model.score(
            self.preprocessor.transform(X), 
            y
        )
        
        return {
            'mse': mse,
            'rmse': rmse,
            'mae': mae,
            'r2': r2
        }


# Demo: House price prediction
if __name__ == "__main__":
    # Synthetic data: price = 50000 + 100*sqft + 20000*bedrooms + noise
    random.seed(42)
    n = 100
    
    X = []
    y = []
    for _ in range(n):
        sqft = random.uniform(800, 3000)
        bedrooms = random.randint(1, 5)
        price = 50000 + 100 * sqft + 20000 * bedrooms + random.gauss(0, 10000)
        
        X.append([sqft, bedrooms])
        y.append(price)
    
    # Create and train pipeline
    pipeline = RegressionPipeline(regularization='l2', lambda_param=0.01)
    
    # Cross-validation
    cv_results = pipeline.cross_validate(X, y, k=5)
    print("Cross-Validation Results:")
    print(f"  Mean R²: {cv_results['mean_r2']:.4f} ± {cv_results['std_r2']:.4f}")
    
    # Train/test split
    X_train, X_test, y_train, y_test = pipeline.train_test_split(X, y, test_size=0.2)
    
    # Fit and evaluate
    pipeline.fit(X_train, y_train)
    metrics = pipeline.evaluate(X_test, y_test)
    
    print("\nTest Set Metrics:")
    print(f"  R²: {metrics['r2']:.4f}")
    print(f"  RMSE: ${metrics['rmse']:.2f}")
    print(f"  MAE: ${metrics['mae']:.2f}")
    
    print("\nLearned Coefficients:")
    print(f"  Intercept: ${pipeline.model.intercept:.2f}")
    print(f"  Price per sqft: ${pipeline.model.coefficients[0]:.2f}")
    print(f"  Price per bedroom: ${pipeline.model.coefficients[1]:.2f}")
```

## 11. Model Diagnostics

### 11.1 Residual Analysis

- **Normality**: Residuals should be normally distributed
- **Homoscedasticity**: Constant variance of residuals
- **Independence**: No autocorrelation
- **Linearity**: No patterns in residual plots

### 11.2 Influential Points

- **Leverage**: Points far from mean of X
- **Cook's Distance**: Influence on all predictions
- **DFBETAS**: Influence on specific coefficients

## 12. Edge Cases

| Scenario | Solution |
|----------|----------|
| Multicollinearity | Ridge regression, remove features |
| Overfitting | Regularization, more data |
| Non-linear data | Polynomial features, other models |
| Outliers | Robust regression, outlier removal |
| Missing values | Imputation, indicator variables |

## 13. References

- [Wikipedia: Linear Regression](https://en.wikipedia.org/wiki/Linear_regression)
- Bishop. "Pattern Recognition and Machine Learning" (Ch. 3)
- Hastie, Tibshirani, Friedman. "Elements of Statistical Learning"
