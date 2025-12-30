# Logistic Regression

## Overview
- **Category**: Supervised Learning - Classification
- **Complexity**: Time: O(ndi) | Space: O(d)
- **Type**: Discriminative, Probabilistic
- **Source File**: [machine_learning/logistic_regression.py](../../../machine_learning/logistic_regression.py)

## 1. Mathematical Foundation

### 1.1 The Sigmoid Function

The sigmoid (logistic) function maps any real number to (0, 1):

$$
\sigma(z) = \frac{1}{1 + e^{-z}}
$$

**Properties**:
- $\sigma(0) = 0.5$
- $\sigma(-\infty) = 0$, $\sigma(+\infty) = 1$
- $\sigma'(z) = \sigma(z)(1 - \sigma(z))$

### 1.2 Model

For binary classification with features $\mathbf{x} \in \mathbb{R}^d$:

$$
P(y=1|\mathbf{x}) = \sigma(\mathbf{w}^T\mathbf{x} + b) = \frac{1}{1 + e^{-(\mathbf{w}^T\mathbf{x} + b)}}
$$

where:
- $\mathbf{w} \in \mathbb{R}^d$ is the weight vector
- $b \in \mathbb{R}$ is the bias term

### 1.3 Log-Odds (Logit)

The log-odds is linear in features:

$$
\log\frac{P(y=1|\mathbf{x})}{P(y=0|\mathbf{x})} = \mathbf{w}^T\mathbf{x} + b
$$

This is why it's called "logistic regression" - it's linear regression on log-odds.

### 1.4 Loss Function: Cross-Entropy

For a dataset $\{(\mathbf{x}_i, y_i)\}_{i=1}^n$:

$$
\mathcal{L}(\mathbf{w}, b) = -\frac{1}{n}\sum_{i=1}^{n}\left[y_i\log(\hat{y}_i) + (1-y_i)\log(1-\hat{y}_i)\right]
$$

where $\hat{y}_i = \sigma(\mathbf{w}^T\mathbf{x}_i + b)$.

**Why Cross-Entropy?**
- Maximum likelihood estimation
- Convex (unique global minimum)
- Penalizes confident wrong predictions heavily

### 1.5 Gradient

$$
\frac{\partial \mathcal{L}}{\partial \mathbf{w}} = \frac{1}{n}\sum_{i=1}^{n}(\hat{y}_i - y_i)\mathbf{x}_i
$$

$$
\frac{\partial \mathcal{L}}{\partial b} = \frac{1}{n}\sum_{i=1}^{n}(\hat{y}_i - y_i)
$$

### 1.6 Regularization

**L2 Regularization (Ridge)**:
$$
\mathcal{L}_{reg} = \mathcal{L} + \frac{\lambda}{2}\|\mathbf{w}\|^2
$$

**L1 Regularization (Lasso)**:
$$
\mathcal{L}_{reg} = \mathcal{L} + \lambda\|\mathbf{w}\|_1
$$

## 2. Pseudocode

```
ALGORITHM LogisticRegression(X, y, α, λ, max_iter)
    INPUT: Data X (n×d), labels y, learning rate α, regularization λ
    OUTPUT: Weights w, bias b
    
    // Initialize
    w ← zeros(d)
    b ← 0
    
    for iter ← 1 to max_iter do
        // Forward pass
        z ← X · w + b
        predictions ← sigmoid(z)
        
        // Compute gradients
        error ← predictions - y
        grad_w ← (1/n) · X^T · error + λ · w
        grad_b ← (1/n) · sum(error)
        
        // Update
        w ← w - α · grad_w
        b ← b - α · grad_b
        
        // Optional: check convergence
        if gradient norm < tolerance then
            break
    
    return w, b

ALGORITHM Predict(x, w, b, threshold)
    z ← w^T · x + b
    prob ← sigmoid(z)
    return 1 if prob ≥ threshold else 0

ALGORITHM Sigmoid(z)
    return 1 / (1 + exp(-z))
```

## 3. Step-by-Step Example

### Example: Binary Classification

**Data**:
| $x_1$ | $x_2$ | $y$ |
|-------|-------|-----|
| 1 | 2 | 0 |
| 2 | 1 | 0 |
| 4 | 5 | 1 |
| 5 | 4 | 1 |

**Iteration 0** ($w=[0,0]$, $b=0$):
- All predictions = $\sigma(0) = 0.5$
- Loss = $-\frac{1}{4}[2 \cdot \log(0.5) + 2 \cdot \log(0.5)] = 0.693$

**After training**: $w \approx [0.8, 0.8]$, $b \approx -3.6$
- Decision boundary: $0.8x_1 + 0.8x_2 - 3.6 = 0$
- Simplifies to: $x_1 + x_2 = 4.5$

## 4. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Forward pass | O(nd) | O(n) |
| Gradient computation | O(nd) | O(d) |
| Single update | O(d) | O(d) |
| Training (i iterations) | O(ndi) | O(nd) |
| Prediction | O(d) | O(1) |

## 5. Visual Representation

### 5.1 Sigmoid Function

```
P(y=1) │
   1.0 ├──────────────────────────●●●●●
       │                      ●●●
   0.5 ├─────────────────●●●─────────
       │              ●●●
   0.0 ├●●●●●●●●●●●●●●───────────────
       └────────────────┼─────────────→ z
                        0
```

### 5.2 Decision Boundary

```
x₂ │
   │   × × ×      ○ ○ ○
   │     × ×    ○ ○
   │       × \ ○
   │         \
   │    ×      \ ○
   │            \   ○
   └─────────────\──────→ x₁
                  Decision
                  Boundary
    × = Class 0   ○ = Class 1
```

## 6. Implementation

```python
from typing import List, Tuple, Optional
import math
import random


class LogisticRegression:
    """
    Logistic Regression classifier.
    
    Binary classification using sigmoid function and
    gradient descent optimization.
    """
    
    def __init__(
        self,
        learning_rate: float = 0.01,
        max_iterations: int = 1000,
        regularization: float = 0.0,
        tolerance: float = 1e-4
    ):
        """
        Initialize logistic regression.
        
        Args:
            learning_rate: Step size for gradient descent
            max_iterations: Maximum training iterations
            regularization: L2 regularization strength
            tolerance: Convergence tolerance
        """
        self.lr = learning_rate
        self.max_iter = max_iterations
        self.reg = regularization
        self.tol = tolerance
        
        self.weights: List[float] = []
        self.bias: float = 0.0
        self.losses: List[float] = []
    
    @staticmethod
    def sigmoid(z: float) -> float:
        """
        Sigmoid activation function.
        
        >>> LogisticRegression.sigmoid(0)
        0.5
        >>> abs(LogisticRegression.sigmoid(100) - 1.0) < 1e-10
        True
        >>> abs(LogisticRegression.sigmoid(-100)) < 1e-10
        True
        """
        # Clip to prevent overflow
        z = max(-500, min(500, z))
        return 1.0 / (1.0 + math.exp(-z))
    
    def _predict_proba_single(self, x: List[float]) -> float:
        """Predict probability for single sample."""
        z = sum(w * xi for w, xi in zip(self.weights, x)) + self.bias
        return self.sigmoid(z)
    
    def _compute_loss(
        self,
        X: List[List[float]],
        y: List[int]
    ) -> float:
        """Compute cross-entropy loss with regularization."""
        n = len(y)
        loss = 0.0
        
        for xi, yi in zip(X, y):
            pred = self._predict_proba_single(xi)
            # Clip predictions to avoid log(0)
            pred = max(1e-15, min(1 - 1e-15, pred))
            
            loss -= yi * math.log(pred) + (1 - yi) * math.log(1 - pred)
        
        loss /= n
        
        # L2 regularization
        if self.reg > 0:
            loss += (self.reg / 2) * sum(w ** 2 for w in self.weights)
        
        return loss
    
    def _compute_gradients(
        self,
        X: List[List[float]],
        y: List[int]
    ) -> Tuple[List[float], float]:
        """Compute gradients for weights and bias."""
        n = len(y)
        d = len(self.weights)
        
        grad_w = [0.0] * d
        grad_b = 0.0
        
        for xi, yi in zip(X, y):
            pred = self._predict_proba_single(xi)
            error = pred - yi
            
            for j in range(d):
                grad_w[j] += error * xi[j]
            grad_b += error
        
        # Average and add regularization
        for j in range(d):
            grad_w[j] = grad_w[j] / n + self.reg * self.weights[j]
        grad_b /= n
        
        return grad_w, grad_b
    
    def fit(
        self,
        X: List[List[float]],
        y: List[int]
    ) -> 'LogisticRegression':
        """
        Train logistic regression model.
        
        >>> lr = LogisticRegression(learning_rate=0.5, max_iterations=100)
        >>> X = [[1, 2], [2, 1], [4, 5], [5, 4]]
        >>> y = [0, 0, 1, 1]
        >>> lr.fit(X, y)  # doctest: +ELLIPSIS
        <...LogisticRegression object at ...>
        >>> lr.predict([[3, 3]])
        [1]
        """
        n = len(X)
        d = len(X[0])
        
        # Initialize weights
        self.weights = [0.0] * d
        self.bias = 0.0
        self.losses = []
        
        prev_loss = float('inf')
        
        for iteration in range(self.max_iter):
            # Compute loss (optional, for monitoring)
            loss = self._compute_loss(X, y)
            self.losses.append(loss)
            
            # Check convergence
            if abs(prev_loss - loss) < self.tol:
                break
            prev_loss = loss
            
            # Compute gradients
            grad_w, grad_b = self._compute_gradients(X, y)
            
            # Update weights
            for j in range(d):
                self.weights[j] -= self.lr * grad_w[j]
            self.bias -= self.lr * grad_b
        
        return self
    
    def predict_proba(
        self,
        X: List[List[float]]
    ) -> List[float]:
        """
        Predict probabilities.
        
        >>> lr = LogisticRegression()
        >>> lr.weights = [1.0, 1.0]
        >>> lr.bias = -3.0
        >>> probs = lr.predict_proba([[1, 1], [2, 2]])
        >>> probs[0] < 0.5 and probs[1] > 0.5
        True
        """
        return [self._predict_proba_single(x) for x in X]
    
    def predict(
        self,
        X: List[List[float]],
        threshold: float = 0.5
    ) -> List[int]:
        """
        Predict class labels.
        
        >>> lr = LogisticRegression()
        >>> lr.weights = [1.0, 1.0]
        >>> lr.bias = -3.0
        >>> lr.predict([[0, 0], [5, 5]])
        [0, 1]
        """
        probs = self.predict_proba(X)
        return [1 if p >= threshold else 0 for p in probs]
    
    def score(
        self,
        X: List[List[float]],
        y: List[int]
    ) -> float:
        """
        Compute accuracy.
        
        >>> lr = LogisticRegression()
        >>> lr.weights = [1.0, 1.0]
        >>> lr.bias = -3.0
        >>> lr.score([[0, 0], [5, 5]], [0, 1])
        1.0
        """
        predictions = self.predict(X)
        correct = sum(1 for pred, actual in zip(predictions, y) if pred == actual)
        return correct / len(y)


class LogisticRegressionSGD:
    """
    Logistic Regression with Stochastic Gradient Descent.
    
    Better for large datasets.
    """
    
    def __init__(
        self,
        learning_rate: float = 0.01,
        n_epochs: int = 100,
        batch_size: int = 32,
        regularization: float = 0.0
    ):
        self.lr = learning_rate
        self.n_epochs = n_epochs
        self.batch_size = batch_size
        self.reg = regularization
        
        self.weights: List[float] = []
        self.bias: float = 0.0
    
    @staticmethod
    def sigmoid(z: float) -> float:
        z = max(-500, min(500, z))
        return 1.0 / (1.0 + math.exp(-z))
    
    def fit(
        self,
        X: List[List[float]],
        y: List[int]
    ) -> 'LogisticRegressionSGD':
        """
        Train with SGD.
        
        >>> lr = LogisticRegressionSGD(n_epochs=50)
        >>> X = [[1, 2], [2, 1], [4, 5], [5, 4]]
        >>> y = [0, 0, 1, 1]
        >>> lr.fit(X, y)  # doctest: +ELLIPSIS
        <...LogisticRegressionSGD object at ...>
        """
        n = len(X)
        d = len(X[0])
        
        self.weights = [0.0] * d
        self.bias = 0.0
        
        indices = list(range(n))
        
        for epoch in range(self.n_epochs):
            random.shuffle(indices)
            
            for i in range(0, n, self.batch_size):
                batch_indices = indices[i:i + self.batch_size]
                batch_size = len(batch_indices)
                
                grad_w = [0.0] * d
                grad_b = 0.0
                
                for idx in batch_indices:
                    xi, yi = X[idx], y[idx]
                    z = sum(w * xij for w, xij in zip(self.weights, xi)) + self.bias
                    pred = self.sigmoid(z)
                    error = pred - yi
                    
                    for j in range(d):
                        grad_w[j] += error * xi[j]
                    grad_b += error
                
                # Average and update
                for j in range(d):
                    grad_w[j] = grad_w[j] / batch_size + self.reg * self.weights[j]
                    self.weights[j] -= self.lr * grad_w[j]
                self.bias -= self.lr * (grad_b / batch_size)
        
        return self
    
    def predict(
        self,
        X: List[List[float]],
        threshold: float = 0.5
    ) -> List[int]:
        """Predict class labels."""
        predictions = []
        for x in X:
            z = sum(w * xi for w, xi in zip(self.weights, x)) + self.bias
            prob = self.sigmoid(z)
            predictions.append(1 if prob >= threshold else 0)
        return predictions


class MulticlassLogisticRegression:
    """
    Multiclass Logistic Regression using One-vs-Rest.
    
    Trains one binary classifier per class.
    """
    
    def __init__(
        self,
        learning_rate: float = 0.01,
        max_iterations: int = 500,
        regularization: float = 0.0
    ):
        self.lr = learning_rate
        self.max_iter = max_iterations
        self.reg = regularization
        
        self.classifiers: dict = {}
        self.classes: List[int] = []
    
    def fit(
        self,
        X: List[List[float]],
        y: List[int]
    ) -> 'MulticlassLogisticRegression':
        """
        Train one-vs-rest classifiers.
        
        >>> mlr = MulticlassLogisticRegression(max_iterations=100)
        >>> X = [[1, 0], [0, 1], [5, 5], [6, 5], [10, 0], [9, 1]]
        >>> y = [0, 0, 1, 1, 2, 2]
        >>> mlr.fit(X, y)  # doctest: +ELLIPSIS
        <...MulticlassLogisticRegression object at ...>
        """
        self.classes = sorted(set(y))
        
        for cls in self.classes:
            # Binary labels: 1 if class == cls, else 0
            binary_y = [1 if yi == cls else 0 for yi in y]
            
            classifier = LogisticRegression(
                learning_rate=self.lr,
                max_iterations=self.max_iter,
                regularization=self.reg
            )
            classifier.fit(X, binary_y)
            self.classifiers[cls] = classifier
        
        return self
    
    def predict_proba(
        self,
        X: List[List[float]]
    ) -> List[List[float]]:
        """Predict probabilities for each class."""
        n = len(X)
        k = len(self.classes)
        
        # Get probabilities from each classifier
        probs = []
        for x in X:
            row = []
            for cls in self.classes:
                clf = self.classifiers[cls]
                prob = clf.predict_proba([x])[0]
                row.append(prob)
            
            # Normalize (softmax-style)
            total = sum(row)
            if total > 0:
                row = [p / total for p in row]
            probs.append(row)
        
        return probs
    
    def predict(self, X: List[List[float]]) -> List[int]:
        """
        Predict class labels.
        
        >>> mlr = MulticlassLogisticRegression(max_iterations=100)
        >>> X = [[1, 0], [0, 1], [5, 5], [6, 5], [10, 0], [9, 1]]
        >>> y = [0, 0, 1, 1, 2, 2]
        >>> mlr.fit(X, y)  # doctest: +ELLIPSIS
        <...>
        >>> predictions = mlr.predict([[0.5, 0.5], [5.5, 5.5], [9.5, 0.5]])
        >>> len(predictions)
        3
        """
        probs = self.predict_proba(X)
        predictions = []
        
        for prob_row in probs:
            max_idx = 0
            max_prob = prob_row[0]
            for i, p in enumerate(prob_row):
                if p > max_prob:
                    max_prob = p
                    max_idx = i
            predictions.append(self.classes[max_idx])
        
        return predictions


def compute_metrics(
    y_true: List[int],
    y_pred: List[int]
) -> dict:
    """
    Compute classification metrics.
    
    >>> y_true = [1, 1, 0, 0, 1, 0]
    >>> y_pred = [1, 0, 0, 0, 1, 1]
    >>> m = compute_metrics(y_true, y_pred)
    >>> m['accuracy']
    0.6666666666666666
    """
    # Confusion matrix
    tp = sum(1 for t, p in zip(y_true, y_pred) if t == 1 and p == 1)
    tn = sum(1 for t, p in zip(y_true, y_pred) if t == 0 and p == 0)
    fp = sum(1 for t, p in zip(y_true, y_pred) if t == 0 and p == 1)
    fn = sum(1 for t, p in zip(y_true, y_pred) if t == 1 and p == 0)
    
    accuracy = (tp + tn) / len(y_true)
    precision = tp / (tp + fp) if (tp + fp) > 0 else 0
    recall = tp / (tp + fn) if (tp + fn) > 0 else 0
    f1 = 2 * precision * recall / (precision + recall) if (precision + recall) > 0 else 0
    
    return {
        'accuracy': accuracy,
        'precision': precision,
        'recall': recall,
        'f1': f1,
        'confusion_matrix': {'tp': tp, 'tn': tn, 'fp': fp, 'fn': fn}
    }


def roc_curve(
    y_true: List[int],
    y_scores: List[float]
) -> Tuple[List[float], List[float], List[float]]:
    """
    Compute ROC curve.
    
    Returns (fpr, tpr, thresholds).
    
    >>> y_true = [0, 0, 1, 1]
    >>> y_scores = [0.1, 0.4, 0.6, 0.9]
    >>> fpr, tpr, thresholds = roc_curve(y_true, y_scores)
    >>> len(fpr) > 0
    True
    """
    # Sort by score descending
    sorted_pairs = sorted(zip(y_scores, y_true), reverse=True)
    
    total_positive = sum(y_true)
    total_negative = len(y_true) - total_positive
    
    tpr_list = [0.0]
    fpr_list = [0.0]
    thresholds = [1.0]
    
    tp = 0
    fp = 0
    
    for score, label in sorted_pairs:
        if label == 1:
            tp += 1
        else:
            fp += 1
        
        tpr_list.append(tp / total_positive if total_positive > 0 else 0)
        fpr_list.append(fp / total_negative if total_negative > 0 else 0)
        thresholds.append(score)
    
    return fpr_list, tpr_list, thresholds


def auc(fpr: List[float], tpr: List[float]) -> float:
    """
    Compute Area Under ROC Curve using trapezoidal rule.
    
    >>> fpr = [0, 0, 0.5, 1]
    >>> tpr = [0, 0.5, 0.5, 1]
    >>> 0.6 < auc(fpr, tpr) < 0.8
    True
    """
    area = 0.0
    for i in range(1, len(fpr)):
        area += (fpr[i] - fpr[i-1]) * (tpr[i] + tpr[i-1]) / 2
    return area
```

## 7. Applications

### 7.1 Classic Applications

- Binary classification (spam detection, fraud detection)
- Medical diagnosis (disease prediction)
- Credit scoring
- Click-through rate prediction
- Sentiment analysis

### 7.2 Why Logistic Regression?

| Advantage | Description |
|-----------|-------------|
| Interpretable | Weights show feature importance |
| Probabilistic | Outputs calibrated probabilities |
| Fast | Linear in features |
| Regularizable | Handles high-dim data |
| Baseline | Good first model to try |

## 8. Real-World Software Engineering Applications

### 8.1 Production Example: Spam Detection

```python
from typing import List, Dict, Set, Tuple
import re
import math


class SpamClassifier:
    """
    Spam email classifier using Logistic Regression.
    """
    
    def __init__(
        self,
        max_features: int = 1000,
        ngram_range: Tuple[int, int] = (1, 2)
    ):
        self.max_features = max_features
        self.ngram_range = ngram_range
        
        self.vocabulary: Dict[str, int] = {}
        self.idf: Dict[str, float] = {}
        self.model = LogisticRegression(
            learning_rate=0.1,
            max_iterations=500,
            regularization=0.01
        )
    
    def _tokenize(self, text: str) -> List[str]:
        """Tokenize and normalize text."""
        text = text.lower()
        text = re.sub(r'[^a-z0-9\s]', ' ', text)
        words = text.split()
        
        tokens = []
        
        # Unigrams
        if self.ngram_range[0] <= 1:
            tokens.extend(words)
        
        # Bigrams
        if self.ngram_range[1] >= 2:
            tokens.extend(
                f"{words[i]}_{words[i+1]}" 
                for i in range(len(words) - 1)
            )
        
        return tokens
    
    def _compute_tfidf(
        self,
        documents: List[str],
        fit: bool = True
    ) -> List[List[float]]:
        """Compute TF-IDF features."""
        # Tokenize all documents
        doc_tokens = [self._tokenize(doc) for doc in documents]
        
        if fit:
            # Build vocabulary from term frequency
            term_doc_freq: Dict[str, int] = {}
            
            for tokens in doc_tokens:
                unique_tokens = set(tokens)
                for token in unique_tokens:
                    term_doc_freq[token] = term_doc_freq.get(token, 0) + 1
            
            # Select top features by document frequency
            sorted_terms = sorted(
                term_doc_freq.items(),
                key=lambda x: x[1],
                reverse=True
            )[:self.max_features]
            
            self.vocabulary = {
                term: idx for idx, (term, _) in enumerate(sorted_terms)
            }
            
            # Compute IDF
            n_docs = len(documents)
            self.idf = {
                term: math.log(n_docs / (df + 1))
                for term, df in term_doc_freq.items()
                if term in self.vocabulary
            }
        
        # Compute TF-IDF vectors
        features = []
        for tokens in doc_tokens:
            # Term frequency
            tf: Dict[str, int] = {}
            for token in tokens:
                tf[token] = tf.get(token, 0) + 1
            
            # TF-IDF vector
            vector = [0.0] * len(self.vocabulary)
            max_tf = max(tf.values()) if tf else 1
            
            for term, idx in self.vocabulary.items():
                if term in tf:
                    normalized_tf = tf[term] / max_tf
                    vector[idx] = normalized_tf * self.idf.get(term, 0)
            
            features.append(vector)
        
        return features
    
    def fit(
        self,
        emails: List[str],
        labels: List[int]
    ) -> 'SpamClassifier':
        """
        Train spam classifier.
        
        Args:
            emails: List of email texts
            labels: 1 for spam, 0 for ham
        
        >>> clf = SpamClassifier(max_features=100)
        >>> emails = [
        ...     "Win free money now! Click here!",
        ...     "Meeting at 3pm tomorrow",
        ...     "FREE VIAGRA PILLS!!!",
        ...     "Please review the attached document"
        ... ]
        >>> labels = [1, 0, 1, 0]
        >>> clf.fit(emails, labels)  # doctest: +ELLIPSIS
        <...SpamClassifier object at ...>
        """
        X = self._compute_tfidf(emails, fit=True)
        self.model.fit(X, labels)
        return self
    
    def predict(self, emails: List[str]) -> List[int]:
        """Predict spam (1) or ham (0)."""
        X = self._compute_tfidf(emails, fit=False)
        return self.model.predict(X)
    
    def predict_proba(self, emails: List[str]) -> List[float]:
        """Get spam probability."""
        X = self._compute_tfidf(emails, fit=False)
        return self.model.predict_proba(X)
    
    def get_top_spam_words(self, n: int = 10) -> List[Tuple[str, float]]:
        """Get words most indicative of spam."""
        if not self.model.weights:
            return []
        
        word_weights = []
        for word, idx in self.vocabulary.items():
            word_weights.append((word, self.model.weights[idx]))
        
        # Sort by weight (positive = spam)
        word_weights.sort(key=lambda x: x[1], reverse=True)
        return word_weights[:n]
    
    def get_top_ham_words(self, n: int = 10) -> List[Tuple[str, float]]:
        """Get words most indicative of ham."""
        if not self.model.weights:
            return []
        
        word_weights = []
        for word, idx in self.vocabulary.items():
            word_weights.append((word, self.model.weights[idx]))
        
        # Sort by weight (negative = ham)
        word_weights.sort(key=lambda x: x[1])
        return word_weights[:n]


class CreditScoring:
    """
    Credit risk scoring using Logistic Regression.
    """
    
    def __init__(self):
        self.model = LogisticRegression(
            learning_rate=0.1,
            max_iterations=1000,
            regularization=0.1
        )
        self.feature_names: List[str] = []
        self.feature_means: List[float] = []
        self.feature_stds: List[float] = []
    
    def _standardize(
        self,
        X: List[List[float]],
        fit: bool = True
    ) -> List[List[float]]:
        """Standardize features (z-score normalization)."""
        if not X:
            return []
        
        n = len(X)
        d = len(X[0])
        
        if fit:
            # Compute mean and std
            self.feature_means = []
            self.feature_stds = []
            
            for j in range(d):
                values = [X[i][j] for i in range(n)]
                mean = sum(values) / n
                variance = sum((v - mean) ** 2 for v in values) / n
                std = math.sqrt(variance) if variance > 0 else 1.0
                
                self.feature_means.append(mean)
                self.feature_stds.append(std)
        
        # Standardize
        result = []
        for row in X:
            standardized = [
                (row[j] - self.feature_means[j]) / self.feature_stds[j]
                for j in range(d)
            ]
            result.append(standardized)
        
        return result
    
    def fit(
        self,
        applications: List[Dict[str, float]],
        defaults: List[int]
    ) -> 'CreditScoring':
        """
        Train credit scoring model.
        
        Args:
            applications: List of loan applications with features
            defaults: 1 if defaulted, 0 otherwise
        
        >>> scorer = CreditScoring()
        >>> apps = [
        ...     {'income': 50000, 'debt_ratio': 0.2, 'credit_history': 10},
        ...     {'income': 30000, 'debt_ratio': 0.5, 'credit_history': 2},
        ...     {'income': 80000, 'debt_ratio': 0.1, 'credit_history': 15},
        ...     {'income': 25000, 'debt_ratio': 0.7, 'credit_history': 1}
        ... ]
        >>> defaults = [0, 1, 0, 1]
        >>> scorer.fit(apps, defaults)  # doctest: +ELLIPSIS
        <...CreditScoring object at ...>
        """
        if not applications:
            return self
        
        self.feature_names = list(applications[0].keys())
        
        # Convert to matrix
        X = [[app[name] for name in self.feature_names] for app in applications]
        
        # Standardize
        X_scaled = self._standardize(X, fit=True)
        
        # Train
        self.model.fit(X_scaled, defaults)
        
        return self
    
    def predict_default_probability(
        self,
        applications: List[Dict[str, float]]
    ) -> List[float]:
        """Predict probability of default."""
        X = [[app[name] for name in self.feature_names] for app in applications]
        X_scaled = self._standardize(X, fit=False)
        return self.model.predict_proba(X_scaled)
    
    def credit_decision(
        self,
        application: Dict[str, float],
        threshold: float = 0.3
    ) -> Tuple[str, float, str]:
        """
        Make credit decision.
        
        Returns (decision, probability, explanation).
        """
        prob = self.predict_default_probability([application])[0]
        
        if prob < threshold:
            decision = "APPROVED"
        else:
            decision = "DENIED"
        
        # Generate explanation
        x = [application[name] for name in self.feature_names]
        x_scaled = self._standardize([x], fit=False)[0]
        
        contributions = []
        for i, name in enumerate(self.feature_names):
            contribution = self.model.weights[i] * x_scaled[i]
            contributions.append((name, contribution))
        
        contributions.sort(key=lambda x: abs(x[1]), reverse=True)
        
        reasons = []
        for name, contrib in contributions[:3]:
            if contrib > 0:
                reasons.append(f"High risk: {name}")
            else:
                reasons.append(f"Low risk: {name}")
        
        explanation = "; ".join(reasons)
        
        return decision, prob, explanation
    
    def get_feature_importance(self) -> List[Tuple[str, float]]:
        """Get feature importance (absolute weight values)."""
        importance = [
            (name, abs(self.model.weights[i]))
            for i, name in enumerate(self.feature_names)
        ]
        importance.sort(key=lambda x: x[1], reverse=True)
        return importance


# Demo
if __name__ == "__main__":
    print("Spam Classifier Demo")
    print("=" * 50)
    
    # Training data
    training_emails = [
        "WINNER! You have been selected for a FREE prize!",
        "Meeting rescheduled to 4pm tomorrow",
        "Get CHEAP medications now! No prescription needed!",
        "Please find the quarterly report attached",
        "URGENT: Your account has been compromised, click here!",
        "Can we discuss the project timeline?",
        "FREE FREE FREE! Click now for amazing deals!!!",
        "Thanks for your presentation yesterday"
    ]
    training_labels = [1, 0, 1, 0, 1, 0, 1, 0]  # 1 = spam
    
    spam_clf = SpamClassifier(max_features=200)
    spam_clf.fit(training_emails, training_labels)
    
    # Test emails
    test_emails = [
        "You've won a million dollars!",
        "Lunch meeting at noon?"
    ]
    
    predictions = spam_clf.predict(test_emails)
    probabilities = spam_clf.predict_proba(test_emails)
    
    for email, pred, prob in zip(test_emails, predictions, probabilities):
        label = "SPAM" if pred == 1 else "HAM"
        print(f"Email: {email[:50]}...")
        print(f"  Prediction: {label} (probability: {prob:.2%})")
    
    print("\nTop spam indicators:")
    for word, weight in spam_clf.get_top_spam_words(5):
        print(f"  {word}: {weight:.3f}")
    
    print("\n" + "=" * 50)
    print("Credit Scoring Demo")
    print("=" * 50)
    
    # Training data
    applications = [
        {'income': 75000, 'debt_ratio': 0.2, 'years_employed': 5, 'credit_score': 720},
        {'income': 35000, 'debt_ratio': 0.6, 'years_employed': 1, 'credit_score': 580},
        {'income': 55000, 'debt_ratio': 0.3, 'years_employed': 3, 'credit_score': 680},
        {'income': 28000, 'debt_ratio': 0.7, 'years_employed': 0, 'credit_score': 520},
        {'income': 90000, 'debt_ratio': 0.15, 'years_employed': 10, 'credit_score': 780},
        {'income': 40000, 'debt_ratio': 0.55, 'years_employed': 2, 'credit_score': 600}
    ]
    defaults = [0, 1, 0, 1, 0, 1]
    
    scorer = CreditScoring()
    scorer.fit(applications, defaults)
    
    # Test application
    test_app = {
        'income': 50000,
        'debt_ratio': 0.4,
        'years_employed': 2,
        'credit_score': 650
    }
    
    decision, prob, explanation = scorer.credit_decision(test_app)
    print(f"\nApplication: {test_app}")
    print(f"Decision: {decision}")
    print(f"Default Probability: {prob:.2%}")
    print(f"Explanation: {explanation}")
    
    print("\nFeature Importance:")
    for name, importance in scorer.get_feature_importance():
        print(f"  {name}: {importance:.3f}")
```

## 9. Comparison with Other Classifiers

| Aspect | Logistic Regression | SVM | Random Forest | Neural Network |
|--------|---------------------|-----|---------------|----------------|
| Interpretability | High | Low | Medium | Low |
| Training speed | Fast | Medium | Medium | Slow |
| Prediction speed | Very fast | Fast | Medium | Fast |
| Handles non-linear | No (needs features) | Yes (kernel) | Yes | Yes |
| Probability output | Native | Calibration needed | Yes | Softmax |

## 10. Edge Cases

| Case | Handling |
|------|----------|
| Perfect separation | Regularization |
| Imbalanced classes | Class weights, resampling |
| Multicollinearity | Regularization, feature selection |
| Missing values | Imputation |
| Outliers | Robust scaling |

## 11. Best Practices

1. **Scale features** to similar ranges
2. **Use regularization** for high-dimensional data
3. **Check calibration** of probabilities
4. **Monitor convergence** during training
5. **Use appropriate metrics** (AUC, F1 for imbalanced)

## 12. References

- [Wikipedia: Logistic Regression](https://en.wikipedia.org/wiki/Logistic_regression)
- Bishop, C.M. "Pattern Recognition and Machine Learning" (2006), Chapter 4
- Hastie, T., et al. "Elements of Statistical Learning" (2009), Chapter 4
