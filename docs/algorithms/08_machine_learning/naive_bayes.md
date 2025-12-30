# Naive Bayes Classifier

## Overview
- **Category**: Supervised Learning - Probabilistic Classification
- **Complexity**: Time: O(nd) training | O(kd) prediction | Space: O(kd)
- **Type**: Generative, Probabilistic
- **Source File**: [machine_learning/naive_bayes.py](../../../machine_learning/naive_bayes.py)

## 1. Mathematical Foundation

### 1.1 Bayes' Theorem

$$
P(C|X) = \frac{P(X|C) \cdot P(C)}{P(X)}
$$

where:
- $P(C|X)$ = Posterior probability of class $C$ given features $X$
- $P(X|C)$ = Likelihood of features given class
- $P(C)$ = Prior probability of class
- $P(X)$ = Evidence (normalizing constant)

### 1.2 Naive Independence Assumption

Assume features are conditionally independent given class:

$$
P(X|C) = P(x_1, x_2, \ldots, x_d | C) = \prod_{i=1}^{d} P(x_i | C)
$$

This "naive" assumption makes computation tractable.

### 1.3 Classification Rule

$$
\hat{C} = \underset{c}{\text{argmax}} \; P(C=c) \prod_{i=1}^{d} P(x_i | C=c)
$$

In log space (for numerical stability):
$$
\hat{C} = \underset{c}{\text{argmax}} \left[ \log P(C=c) + \sum_{i=1}^{d} \log P(x_i | C=c) \right]
$$

### 1.4 Variants by Feature Distribution

| Variant | Feature Type | $P(x_i|C)$ |
|---------|--------------|------------|
| Gaussian NB | Continuous | $\mathcal{N}(\mu_{ic}, \sigma_{ic}^2)$ |
| Multinomial NB | Counts | $\frac{N_{ic} + \alpha}{N_c + \alpha d}$ |
| Bernoulli NB | Binary | $p_{ic}^{x_i}(1-p_{ic})^{1-x_i}$ |

### 1.5 Gaussian Naive Bayes

For continuous features:
$$
P(x_i | C=c) = \frac{1}{\sqrt{2\pi\sigma_{ic}^2}} \exp\left(-\frac{(x_i - \mu_{ic})^2}{2\sigma_{ic}^2}\right)
$$

### 1.6 Multinomial Naive Bayes

For count/frequency features (e.g., word counts):
$$
P(x_i | C=c) = \frac{N_{ic} + \alpha}{N_c + \alpha d}
$$

where:
- $N_{ic}$ = count of feature $i$ in class $c$
- $N_c$ = total count in class $c$
- $\alpha$ = smoothing parameter (Laplace smoothing when $\alpha=1$)

## 2. Pseudocode

```
ALGORITHM TrainGaussianNB(X, y)
    INPUT: Training data X (n×d), labels y
    OUTPUT: Model parameters (priors, means, variances)
    
    classes ← unique values in y
    
    for each class c in classes do
        // Prior probability
        priors[c] ← count(y = c) / n
        
        // Class samples
        X_c ← X where y = c
        
        for each feature i do
            // Mean and variance per feature per class
            means[c][i] ← mean(X_c[:, i])
            vars[c][i] ← variance(X_c[:, i]) + ε  // Add small ε for stability
    
    return priors, means, vars

ALGORITHM PredictGaussianNB(x, priors, means, vars)
    INPUT: Sample x, model parameters
    OUTPUT: Predicted class
    
    best_class ← None
    best_log_prob ← -∞
    
    for each class c do
        // Start with log prior
        log_prob ← log(priors[c])
        
        // Add log likelihood for each feature
        for each feature i do
            log_prob ← log_prob + log_gaussian_pdf(x[i], means[c][i], vars[c][i])
        
        if log_prob > best_log_prob then
            best_log_prob ← log_prob
            best_class ← c
    
    return best_class

ALGORITHM TrainMultinomialNB(X, y, α)
    INPUT: Count data X, labels y, smoothing α
    OUTPUT: Model parameters
    
    classes ← unique values in y
    d ← number of features
    
    for each class c do
        priors[c] ← count(y = c) / n
        X_c ← X where y = c
        
        // Total counts per class
        total[c] ← sum(X_c) + α × d
        
        for each feature i do
            // Feature probability with smoothing
            feature_probs[c][i] ← (sum(X_c[:, i]) + α) / total[c]
    
    return priors, feature_probs
```

## 3. Step-by-Step Example

### Example: Spam Classification

**Training Data**:
| Email | "free" | "money" | "meeting" | Spam? |
|-------|--------|---------|-----------|-------|
| 1 | 2 | 1 | 0 | Yes |
| 2 | 0 | 0 | 2 | No |
| 3 | 1 | 2 | 0 | Yes |
| 4 | 0 | 0 | 1 | No |

**Step 1**: Calculate priors
- P(Spam) = 2/4 = 0.5
- P(Not Spam) = 2/4 = 0.5

**Step 2**: Calculate likelihoods (with Laplace smoothing α=1)
- Total words in Spam: 2+1+0+1+2+0 = 6
- Total words in Not Spam: 0+0+2+0+0+1 = 3

| Word | P(word|Spam) | P(word|Not Spam) |
|------|--------------|------------------|
| free | (3+1)/(6+3) = 4/9 | (0+1)/(3+3) = 1/6 |
| money | (3+1)/(6+3) = 4/9 | (0+1)/(3+3) = 1/6 |
| meeting | (0+1)/(6+3) = 1/9 | (3+1)/(3+3) = 4/6 |

**Step 3**: Classify "free money" (counts: [1, 1, 0])
- P(Spam|X) ∝ 0.5 × (4/9)¹ × (4/9)¹ × (1/9)⁰ = 0.099
- P(Not Spam|X) ∝ 0.5 × (1/6)¹ × (1/6)¹ × (4/6)⁰ = 0.014

**Prediction**: Spam (0.099 > 0.014)

## 4. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Training | O(nd) | O(kd) |
| Prediction (single) | O(kd) | O(1) |
| Prediction (batch) | O(mkd) | O(m) |

Where:
- n = training samples
- d = features
- k = classes
- m = test samples

**Note**: Naive Bayes is one of the fastest classifiers!

## 5. Visual Representation

### 5.1 Gaussian NB Decision Boundary

```
x₂ │
   │    Class A         Class B
   │   ●  ●  ●        ○  ○  ○
   │     ●  ●   \    ○  ○
   │   ●  ●      \  ○  ○  ○
   │     ●  ●     \○  ○
   │   ●            \
   └──────────────────\───────→ x₁
                    Decision
                    Boundary
```

### 5.2 Class Distributions

```
P(x|C) │
       │    Class A        Class B
       │      ╱╲             ╱╲
       │     ╱  ╲           ╱  ╲
       │    ╱    ╲         ╱    ╲
       │   ╱      ╲       ╱      ╲
       │──╱────────╲─────╱────────╲──→ x
          μ_A           μ_B
```

## 6. Implementation

```python
from typing import List, Dict, Tuple, Optional
import math
from collections import Counter, defaultdict


class GaussianNaiveBayes:
    """
    Gaussian Naive Bayes classifier.
    
    Assumes continuous features follow Gaussian distribution.
    """
    
    def __init__(self, var_smoothing: float = 1e-9):
        """
        Initialize Gaussian NB.
        
        Args:
            var_smoothing: Variance smoothing for numerical stability
        """
        self.var_smoothing = var_smoothing
        
        self.classes: List[int] = []
        self.priors: Dict[int, float] = {}
        self.means: Dict[int, List[float]] = {}
        self.variances: Dict[int, List[float]] = {}
        self.n_features: int = 0
    
    def _gaussian_pdf(
        self,
        x: float,
        mean: float,
        var: float
    ) -> float:
        """Compute Gaussian probability density."""
        coeff = 1.0 / math.sqrt(2 * math.pi * var)
        exponent = -((x - mean) ** 2) / (2 * var)
        return coeff * math.exp(exponent)
    
    def _log_gaussian_pdf(
        self,
        x: float,
        mean: float,
        var: float
    ) -> float:
        """Compute log of Gaussian PDF (numerically stable)."""
        return -0.5 * (math.log(2 * math.pi * var) + ((x - mean) ** 2) / var)
    
    def fit(
        self,
        X: List[List[float]],
        y: List[int]
    ) -> 'GaussianNaiveBayes':
        """
        Train Gaussian Naive Bayes.
        
        >>> gnb = GaussianNaiveBayes()
        >>> X = [[1, 2], [2, 1], [3, 4], [4, 3], [5, 6], [6, 5]]
        >>> y = [0, 0, 0, 1, 1, 1]
        >>> gnb.fit(X, y)  # doctest: +ELLIPSIS
        <...GaussianNaiveBayes object at ...>
        >>> gnb.predict([[2, 2], [5, 5]])
        [0, 1]
        """
        n = len(X)
        self.n_features = len(X[0])
        self.classes = sorted(set(y))
        
        # Group samples by class
        class_samples: Dict[int, List[List[float]]] = defaultdict(list)
        for xi, yi in zip(X, y):
            class_samples[yi].append(xi)
        
        for c in self.classes:
            samples = class_samples[c]
            n_c = len(samples)
            
            # Prior
            self.priors[c] = n_c / n
            
            # Mean and variance per feature
            self.means[c] = []
            self.variances[c] = []
            
            for i in range(self.n_features):
                values = [s[i] for s in samples]
                mean = sum(values) / n_c
                var = sum((v - mean) ** 2 for v in values) / n_c
                var += self.var_smoothing  # Stability
                
                self.means[c].append(mean)
                self.variances[c].append(var)
        
        return self
    
    def _predict_log_proba_single(
        self,
        x: List[float]
    ) -> Dict[int, float]:
        """Compute log probabilities for single sample."""
        log_probas = {}
        
        for c in self.classes:
            # Start with log prior
            log_prob = math.log(self.priors[c])
            
            # Add log likelihood for each feature
            for i in range(self.n_features):
                log_prob += self._log_gaussian_pdf(
                    x[i],
                    self.means[c][i],
                    self.variances[c][i]
                )
            
            log_probas[c] = log_prob
        
        return log_probas
    
    def predict_proba(
        self,
        X: List[List[float]]
    ) -> List[List[float]]:
        """
        Predict class probabilities.
        
        >>> gnb = GaussianNaiveBayes()
        >>> gnb.fit([[1], [2], [10], [11]], [0, 0, 1, 1])  # doctest: +ELLIPSIS
        <...>
        >>> probs = gnb.predict_proba([[1.5], [10.5]])
        >>> probs[0][0] > probs[0][1]  # First sample more likely class 0
        True
        >>> probs[1][1] > probs[1][0]  # Second sample more likely class 1
        True
        """
        probas = []
        
        for x in X:
            log_probas = self._predict_log_proba_single(x)
            
            # Convert to probabilities (softmax)
            max_log = max(log_probas.values())
            exp_probas = {c: math.exp(lp - max_log) for c, lp in log_probas.items()}
            total = sum(exp_probas.values())
            
            proba = [exp_probas[c] / total for c in self.classes]
            probas.append(proba)
        
        return probas
    
    def predict(self, X: List[List[float]]) -> List[int]:
        """
        Predict class labels.
        
        >>> gnb = GaussianNaiveBayes()
        >>> gnb.fit([[1], [2], [10], [11]], [0, 0, 1, 1])  # doctest: +ELLIPSIS
        <...>
        >>> gnb.predict([[1.5], [10.5]])
        [0, 1]
        """
        predictions = []
        
        for x in X:
            log_probas = self._predict_log_proba_single(x)
            best_class = max(log_probas.keys(), key=lambda c: log_probas[c])
            predictions.append(best_class)
        
        return predictions
    
    def score(
        self,
        X: List[List[float]],
        y: List[int]
    ) -> float:
        """Compute accuracy."""
        predictions = self.predict(X)
        correct = sum(1 for p, t in zip(predictions, y) if p == t)
        return correct / len(y)


class MultinomialNaiveBayes:
    """
    Multinomial Naive Bayes classifier.
    
    For count/frequency features (e.g., word counts in text).
    """
    
    def __init__(self, alpha: float = 1.0):
        """
        Initialize Multinomial NB.
        
        Args:
            alpha: Laplace smoothing parameter
        """
        self.alpha = alpha
        
        self.classes: List[int] = []
        self.priors: Dict[int, float] = {}
        self.feature_log_probs: Dict[int, List[float]] = {}
        self.n_features: int = 0
    
    def fit(
        self,
        X: List[List[float]],
        y: List[int]
    ) -> 'MultinomialNaiveBayes':
        """
        Train Multinomial Naive Bayes.
        
        >>> mnb = MultinomialNaiveBayes()
        >>> X = [[2, 1, 0], [0, 0, 2], [1, 2, 0], [0, 0, 1]]
        >>> y = [1, 0, 1, 0]  # 1=spam, 0=ham
        >>> mnb.fit(X, y)  # doctest: +ELLIPSIS
        <...MultinomialNaiveBayes object at ...>
        """
        n = len(X)
        self.n_features = len(X[0])
        self.classes = sorted(set(y))
        
        # Group by class
        class_samples: Dict[int, List[List[float]]] = defaultdict(list)
        for xi, yi in zip(X, y):
            class_samples[yi].append(xi)
        
        for c in self.classes:
            samples = class_samples[c]
            
            # Prior
            self.priors[c] = len(samples) / n
            
            # Feature counts
            feature_counts = [
                sum(s[i] for s in samples) + self.alpha
                for i in range(self.n_features)
            ]
            total = sum(feature_counts)
            
            # Log probabilities
            self.feature_log_probs[c] = [
                math.log(count / total) for count in feature_counts
            ]
        
        return self
    
    def predict_log_proba(
        self,
        X: List[List[float]]
    ) -> List[Dict[int, float]]:
        """Predict log probabilities."""
        results = []
        
        for x in X:
            log_probas = {}
            
            for c in self.classes:
                log_prob = math.log(self.priors[c])
                
                for i in range(self.n_features):
                    if x[i] > 0:
                        log_prob += x[i] * self.feature_log_probs[c][i]
                
                log_probas[c] = log_prob
            
            results.append(log_probas)
        
        return results
    
    def predict(self, X: List[List[float]]) -> List[int]:
        """
        Predict class labels.
        
        >>> mnb = MultinomialNaiveBayes()
        >>> X = [[2, 1, 0], [0, 0, 2], [1, 2, 0], [0, 0, 1]]
        >>> y = [1, 0, 1, 0]
        >>> mnb.fit(X, y)  # doctest: +ELLIPSIS
        <...>
        >>> mnb.predict([[1, 1, 0], [0, 0, 1]])
        [1, 0]
        """
        log_probas = self.predict_log_proba(X)
        return [
            max(lp.keys(), key=lambda c: lp[c])
            for lp in log_probas
        ]


class BernoulliNaiveBayes:
    """
    Bernoulli Naive Bayes classifier.
    
    For binary features (presence/absence).
    """
    
    def __init__(self, alpha: float = 1.0):
        """
        Initialize Bernoulli NB.
        
        Args:
            alpha: Smoothing parameter
        """
        self.alpha = alpha
        
        self.classes: List[int] = []
        self.priors: Dict[int, float] = {}
        self.feature_probs: Dict[int, List[float]] = {}
        self.n_features: int = 0
    
    def fit(
        self,
        X: List[List[int]],
        y: List[int]
    ) -> 'BernoulliNaiveBayes':
        """
        Train Bernoulli Naive Bayes.
        
        >>> bnb = BernoulliNaiveBayes()
        >>> X = [[1, 1, 0], [0, 0, 1], [1, 1, 0], [0, 0, 1]]
        >>> y = [1, 0, 1, 0]
        >>> bnb.fit(X, y)  # doctest: +ELLIPSIS
        <...BernoulliNaiveBayes object at ...>
        """
        n = len(X)
        self.n_features = len(X[0])
        self.classes = sorted(set(y))
        
        class_samples: Dict[int, List[List[int]]] = defaultdict(list)
        for xi, yi in zip(X, y):
            class_samples[yi].append(xi)
        
        for c in self.classes:
            samples = class_samples[c]
            n_c = len(samples)
            
            self.priors[c] = n_c / n
            
            # P(feature=1|class) with smoothing
            self.feature_probs[c] = [
                (sum(s[i] for s in samples) + self.alpha) / (n_c + 2 * self.alpha)
                for i in range(self.n_features)
            ]
        
        return self
    
    def predict(self, X: List[List[int]]) -> List[int]:
        """
        Predict class labels.
        
        >>> bnb = BernoulliNaiveBayes()
        >>> X = [[1, 1, 0], [0, 0, 1], [1, 1, 0], [0, 0, 1]]
        >>> y = [1, 0, 1, 0]
        >>> bnb.fit(X, y)  # doctest: +ELLIPSIS
        <...>
        >>> bnb.predict([[1, 1, 0], [0, 0, 1]])
        [1, 0]
        """
        predictions = []
        
        for x in X:
            best_class = None
            best_log_prob = float('-inf')
            
            for c in self.classes:
                log_prob = math.log(self.priors[c])
                
                for i in range(self.n_features):
                    p = self.feature_probs[c][i]
                    if x[i] == 1:
                        log_prob += math.log(p)
                    else:
                        log_prob += math.log(1 - p)
                
                if log_prob > best_log_prob:
                    best_log_prob = log_prob
                    best_class = c
            
            predictions.append(best_class)
        
        return predictions


class ComplementNaiveBayes:
    """
    Complement Naive Bayes for imbalanced datasets.
    
    Uses complement of each class for parameter estimation.
    """
    
    def __init__(self, alpha: float = 1.0):
        self.alpha = alpha
        self.classes: List[int] = []
        self.feature_log_probs: Dict[int, List[float]] = {}
        self.n_features: int = 0
    
    def fit(
        self,
        X: List[List[float]],
        y: List[int]
    ) -> 'ComplementNaiveBayes':
        """
        Train Complement Naive Bayes.
        
        >>> cnb = ComplementNaiveBayes()
        >>> X = [[1, 0], [1, 1], [0, 1], [0, 1], [0, 1], [0, 1]]  # Imbalanced
        >>> y = [0, 0, 1, 1, 1, 1]  # More class 1
        >>> cnb.fit(X, y)  # doctest: +ELLIPSIS
        <...ComplementNaiveBayes object at ...>
        """
        n = len(X)
        self.n_features = len(X[0])
        self.classes = sorted(set(y))
        
        # Group by class
        class_samples: Dict[int, List[List[float]]] = defaultdict(list)
        for xi, yi in zip(X, y):
            class_samples[yi].append(xi)
        
        for c in self.classes:
            # Use complement (all other classes)
            complement_samples = [
                xi for xi, yi in zip(X, y) if yi != c
            ]
            
            if not complement_samples:
                # Only one class
                self.feature_log_probs[c] = [0.0] * self.n_features
                continue
            
            # Feature counts from complement
            feature_counts = [
                sum(s[i] for s in complement_samples) + self.alpha
                for i in range(self.n_features)
            ]
            total = sum(feature_counts)
            
            # Negative log probability (for scoring)
            self.feature_log_probs[c] = [
                -math.log(count / total) for count in feature_counts
            ]
            
            # Normalize weights
            norm = sum(abs(w) for w in self.feature_log_probs[c])
            if norm > 0:
                self.feature_log_probs[c] = [
                    w / norm for w in self.feature_log_probs[c]
                ]
        
        return self
    
    def predict(self, X: List[List[float]]) -> List[int]:
        """Predict class labels."""
        predictions = []
        
        for x in X:
            scores = {}
            
            for c in self.classes:
                # Higher score = more likely class c
                score = sum(
                    x[i] * self.feature_log_probs[c][i]
                    for i in range(self.n_features)
                )
                scores[c] = score
            
            best_class = max(scores.keys(), key=lambda c: scores[c])
            predictions.append(best_class)
        
        return predictions
```

## 7. Applications

### 7.1 Classic Applications

- **Text Classification**: Spam filtering, sentiment analysis, topic categorization
- **Document Categorization**: News classification, email routing
- **Medical Diagnosis**: Disease prediction from symptoms
- **Real-time Prediction**: When speed is critical

### 7.2 When to Use Naive Bayes

| Scenario | Suitability |
|----------|-------------|
| Text classification | ✅ Excellent |
| Small dataset | ✅ Good |
| Many features | ✅ Good |
| Real-time prediction | ✅ Excellent |
| Complex feature dependencies | ❌ Poor |
| Probability calibration needed | ❌ Poor |

## 8. Real-World Software Engineering Applications

### 8.1 Production Example: Text Classifier

```python
from typing import List, Dict, Tuple, Set
import re
import math
from collections import Counter


class TextClassifier:
    """
    Production text classifier using Naive Bayes.
    
    Supports document classification with TF-IDF features.
    """
    
    def __init__(
        self,
        max_features: int = 5000,
        ngram_range: Tuple[int, int] = (1, 2),
        min_df: int = 2
    ):
        """
        Initialize text classifier.
        
        Args:
            max_features: Maximum vocabulary size
            ngram_range: (min_n, max_n) for n-grams
            min_df: Minimum document frequency for terms
        """
        self.max_features = max_features
        self.ngram_range = ngram_range
        self.min_df = min_df
        
        self.vocabulary: Dict[str, int] = {}
        self.idf: Dict[str, float] = {}
        self.model = MultinomialNaiveBayes(alpha=1.0)
        self.classes: List[str] = []
    
    def _tokenize(self, text: str) -> List[str]:
        """Tokenize and extract n-grams."""
        # Normalize
        text = text.lower()
        text = re.sub(r'[^a-z0-9\s]', ' ', text)
        words = text.split()
        
        tokens = []
        
        # Generate n-grams
        for n in range(self.ngram_range[0], self.ngram_range[1] + 1):
            for i in range(len(words) - n + 1):
                ngram = '_'.join(words[i:i+n])
                tokens.append(ngram)
        
        return tokens
    
    def _build_vocabulary(
        self,
        documents: List[str]
    ) -> None:
        """Build vocabulary from documents."""
        # Count document frequency
        doc_freq: Counter = Counter()
        
        for doc in documents:
            tokens = set(self._tokenize(doc))
            doc_freq.update(tokens)
        
        # Filter by min_df and select top features
        valid_terms = [
            term for term, freq in doc_freq.items()
            if freq >= self.min_df
        ]
        
        # Sort by frequency, take top
        valid_terms.sort(key=lambda t: doc_freq[t], reverse=True)
        valid_terms = valid_terms[:self.max_features]
        
        # Create vocabulary
        self.vocabulary = {term: idx for idx, term in enumerate(valid_terms)}
        
        # Compute IDF
        n_docs = len(documents)
        self.idf = {
            term: math.log(n_docs / (doc_freq[term] + 1)) + 1
            for term in self.vocabulary
        }
    
    def _vectorize(
        self,
        documents: List[str],
        fit: bool = False
    ) -> List[List[float]]:
        """Convert documents to TF-IDF vectors."""
        if fit:
            self._build_vocabulary(documents)
        
        vectors = []
        
        for doc in documents:
            tokens = self._tokenize(doc)
            tf = Counter(tokens)
            
            vector = [0.0] * len(self.vocabulary)
            
            for term, idx in self.vocabulary.items():
                if term in tf:
                    # TF-IDF
                    vector[idx] = tf[term] * self.idf.get(term, 1.0)
            
            vectors.append(vector)
        
        return vectors
    
    def fit(
        self,
        documents: List[str],
        labels: List[str]
    ) -> 'TextClassifier':
        """
        Train text classifier.
        
        >>> clf = TextClassifier(max_features=100)
        >>> docs = [
        ...     "I love this product, amazing!",
        ...     "Great quality, highly recommend",
        ...     "Terrible, waste of money",
        ...     "Poor quality, very disappointed"
        ... ]
        >>> labels = ['positive', 'positive', 'negative', 'negative']
        >>> clf.fit(docs, labels)  # doctest: +ELLIPSIS
        <...TextClassifier object at ...>
        """
        self.classes = sorted(set(labels))
        label_map = {c: i for i, c in enumerate(self.classes)}
        
        X = self._vectorize(documents, fit=True)
        y = [label_map[label] for label in labels]
        
        self.model.fit(X, y)
        
        return self
    
    def predict(self, documents: List[str]) -> List[str]:
        """
        Predict document labels.
        
        >>> clf = TextClassifier(max_features=100)
        >>> clf.fit(
        ...     ["good great", "bad terrible", "excellent wonderful", "awful poor"],
        ...     ['pos', 'neg', 'pos', 'neg']
        ... )  # doctest: +ELLIPSIS
        <...>
        >>> clf.predict(["great product"])
        ['pos']
        """
        X = self._vectorize(documents, fit=False)
        predictions = self.model.predict(X)
        return [self.classes[p] for p in predictions]
    
    def predict_proba(
        self,
        documents: List[str]
    ) -> List[Dict[str, float]]:
        """Predict probabilities per class."""
        X = self._vectorize(documents, fit=False)
        log_probas = self.model.predict_log_proba(X)
        
        results = []
        for lp in log_probas:
            # Softmax to convert log probabilities
            max_lp = max(lp.values())
            exp_probs = {c: math.exp(lp[i] - max_lp) for i, c in enumerate(self.classes)}
            total = sum(exp_probs.values())
            probs = {c: p / total for c, p in exp_probs.items()}
            results.append(probs)
        
        return results
    
    def get_top_features(
        self,
        class_name: str,
        n: int = 10
    ) -> List[Tuple[str, float]]:
        """Get most important features for a class."""
        class_idx = self.classes.index(class_name)
        
        # Get log probabilities
        log_probs = self.model.feature_log_probs[class_idx]
        
        # Pair with terms
        term_scores = [
            (term, log_probs[idx])
            for term, idx in self.vocabulary.items()
        ]
        
        # Sort by score
        term_scores.sort(key=lambda x: x[1], reverse=True)
        
        return term_scores[:n]


class SentimentAnalyzer:
    """
    Sentiment analysis for product reviews.
    """
    
    def __init__(self):
        self.classifier = TextClassifier(
            max_features=10000,
            ngram_range=(1, 3),
            min_df=2
        )
    
    def fit(
        self,
        reviews: List[str],
        sentiments: List[str]
    ) -> 'SentimentAnalyzer':
        """
        Train sentiment analyzer.
        
        >>> sa = SentimentAnalyzer()
        >>> reviews = [
        ...     "Excellent product, works perfectly!",
        ...     "Great value for money",
        ...     "Terrible quality, broke immediately",
        ...     "Waste of money, don't buy"
        ... ]
        >>> sentiments = ['positive', 'positive', 'negative', 'negative']
        >>> sa.fit(reviews, sentiments)  # doctest: +ELLIPSIS
        <...SentimentAnalyzer object at ...>
        """
        self.classifier.fit(reviews, sentiments)
        return self
    
    def analyze(
        self,
        review: str
    ) -> Dict[str, any]:
        """
        Analyze sentiment of a review.
        
        Returns sentiment, confidence, and key phrases.
        """
        sentiment = self.classifier.predict([review])[0]
        probas = self.classifier.predict_proba([review])[0]
        confidence = probas[sentiment]
        
        # Extract key n-grams
        tokens = self.classifier._tokenize(review)
        
        # Get feature importances
        sentiment_idx = self.classifier.classes.index(sentiment)
        log_probs = self.classifier.model.feature_log_probs[sentiment_idx]
        
        key_phrases = []
        for token in set(tokens):
            if token in self.classifier.vocabulary:
                idx = self.classifier.vocabulary[token]
                score = log_probs[idx]
                key_phrases.append((token.replace('_', ' '), score))
        
        key_phrases.sort(key=lambda x: x[1], reverse=True)
        
        return {
            'sentiment': sentiment,
            'confidence': confidence,
            'probabilities': probas,
            'key_phrases': key_phrases[:5]
        }
    
    def batch_analyze(
        self,
        reviews: List[str]
    ) -> Dict[str, any]:
        """Analyze multiple reviews with statistics."""
        sentiments = self.classifier.predict(reviews)
        
        sentiment_counts = Counter(sentiments)
        total = len(reviews)
        
        return {
            'total_reviews': total,
            'sentiment_distribution': {
                s: count / total
                for s, count in sentiment_counts.items()
            },
            'positive_count': sentiment_counts.get('positive', 0),
            'negative_count': sentiment_counts.get('negative', 0)
        }


class SpamFilter:
    """
    Email spam filter using Naive Bayes.
    """
    
    def __init__(self, threshold: float = 0.8):
        """
        Initialize spam filter.
        
        Args:
            threshold: Probability threshold for spam classification
        """
        self.threshold = threshold
        self.classifier = MultinomialNaiveBayes(alpha=0.5)
        self.vocabulary: Dict[str, int] = {}
        self.spam_words: Set[str] = set()
    
    def _extract_features(
        self,
        emails: List[Dict],
        fit: bool = False
    ) -> List[List[float]]:
        """Extract features from emails."""
        if fit:
            # Build vocabulary from subjects and bodies
            word_counts: Counter = Counter()
            
            for email in emails:
                text = f"{email.get('subject', '')} {email.get('body', '')}"
                words = re.findall(r'\b[a-z]+\b', text.lower())
                word_counts.update(words)
            
            # Top words as features
            top_words = [w for w, _ in word_counts.most_common(1000)]
            self.vocabulary = {w: i for i, w in enumerate(top_words)}
        
        features = []
        
        for email in emails:
            text = f"{email.get('subject', '')} {email.get('body', '')}"
            words = re.findall(r'\b[a-z]+\b', text.lower())
            word_counts = Counter(words)
            
            # Feature vector
            vector = [0.0] * len(self.vocabulary)
            for word, idx in self.vocabulary.items():
                vector[idx] = word_counts.get(word, 0)
            
            features.append(vector)
        
        return features
    
    def fit(
        self,
        emails: List[Dict],
        is_spam: List[int]
    ) -> 'SpamFilter':
        """
        Train spam filter.
        
        >>> sf = SpamFilter()
        >>> emails = [
        ...     {'subject': 'Meeting', 'body': 'Project discussion tomorrow'},
        ...     {'subject': 'FREE MONEY', 'body': 'Click here to win!!!'},
        ... ]
        >>> is_spam = [0, 1]
        >>> sf.fit(emails, is_spam)  # doctest: +ELLIPSIS
        <...SpamFilter object at ...>
        """
        X = self._extract_features(emails, fit=True)
        self.classifier.fit(X, is_spam)
        
        # Identify spam-indicative words
        spam_log_probs = self.classifier.feature_log_probs.get(1, [])
        ham_log_probs = self.classifier.feature_log_probs.get(0, [])
        
        if spam_log_probs and ham_log_probs:
            for word, idx in self.vocabulary.items():
                if spam_log_probs[idx] > ham_log_probs[idx]:
                    self.spam_words.add(word)
        
        return self
    
    def classify(
        self,
        email: Dict
    ) -> Tuple[bool, float, List[str]]:
        """
        Classify email as spam or ham.
        
        Returns (is_spam, spam_probability, suspicious_words).
        """
        X = self._extract_features([email], fit=False)
        
        log_probas = self.classifier.predict_log_proba(X)[0]
        
        # Convert to probability
        max_lp = max(log_probas.values())
        exp_probs = {c: math.exp(lp - max_lp) for c, lp in log_probas.items()}
        total = sum(exp_probs.values())
        spam_prob = exp_probs.get(1, 0) / total
        
        is_spam = spam_prob >= self.threshold
        
        # Find suspicious words
        text = f"{email.get('subject', '')} {email.get('body', '')}"
        words = set(re.findall(r'\b[a-z]+\b', text.lower()))
        suspicious = list(words & self.spam_words)
        
        return is_spam, spam_prob, suspicious


# Demo
if __name__ == "__main__":
    print("Sentiment Analysis Demo")
    print("=" * 50)
    
    # Training data
    reviews = [
        "This product is amazing! Best purchase ever!",
        "Absolutely love it, exceeded expectations",
        "Great quality and fast shipping",
        "Highly recommend to everyone",
        "Terrible product, complete waste of money",
        "Very disappointed, poor quality",
        "Broke after one day, don't buy",
        "Worst purchase I've ever made"
    ]
    sentiments = ['positive', 'positive', 'positive', 'positive',
                  'negative', 'negative', 'negative', 'negative']
    
    analyzer = SentimentAnalyzer()
    analyzer.fit(reviews, sentiments)
    
    # Analyze new review
    test_review = "Good product but shipping was slow"
    result = analyzer.analyze(test_review)
    
    print(f"Review: {test_review}")
    print(f"Sentiment: {result['sentiment']}")
    print(f"Confidence: {result['confidence']:.2%}")
    print(f"Key phrases: {[kp[0] for kp in result['key_phrases']]}")
    
    print("\n" + "=" * 50)
    print("Spam Filter Demo")
    print("=" * 50)
    
    # Training emails
    training_emails = [
        {'subject': 'Project Update', 'body': 'Please review the attached document'},
        {'subject': 'Meeting Tomorrow', 'body': 'Can we discuss the timeline?'},
        {'subject': 'FREE MONEY NOW!!!', 'body': 'Click here to claim your prize'},
        {'subject': 'YOU WON!!!', 'body': 'Congratulations! Send bank details'},
        {'subject': 'Lunch?', 'body': 'Want to grab lunch today?'},
        {'subject': 'URGENT: Verify Account', 'body': 'Your account has been compromised click now'}
    ]
    is_spam = [0, 0, 1, 1, 0, 1]
    
    spam_filter = SpamFilter(threshold=0.6)
    spam_filter.fit(training_emails, is_spam)
    
    # Test emails
    test_emails = [
        {'subject': 'Quick question', 'body': 'Do you have time for a call?'},
        {'subject': 'FREE VIAGRA', 'body': 'Limited offer! Click now!!!'}
    ]
    
    for email in test_emails:
        is_spam, prob, suspicious = spam_filter.classify(email)
        status = "SPAM" if is_spam else "HAM"
        print(f"\nSubject: {email['subject']}")
        print(f"Classification: {status} ({prob:.1%} spam probability)")
        if suspicious:
            print(f"Suspicious words: {suspicious[:5]}")
```

## 9. Comparison with Other Classifiers

| Aspect | Naive Bayes | Logistic Regression | SVM | Random Forest |
|--------|-------------|---------------------|-----|---------------|
| Training speed | Very fast | Fast | Medium | Medium |
| Prediction speed | Very fast | Very fast | Fast | Medium |
| Memory | Low | Low | Medium | High |
| Interpretability | Medium | High | Low | Medium |
| Probability quality | Poor | Good | Requires calibration | Medium |

## 10. Edge Cases and Handling

| Issue | Solution |
|-------|----------|
| Zero probability | Laplace smoothing |
| Unknown features | Ignore or smooth |
| Numerical underflow | Log probabilities |
| Imbalanced classes | Complement NB, prior adjustment |
| Correlated features | Feature selection |

## 11. References

- [Wikipedia: Naive Bayes Classifier](https://en.wikipedia.org/wiki/Naive_Bayes_classifier)
- McCallum, A. and Nigam, K. "A Comparison of Event Models for Naive Bayes Text Classification" (1998)
- Manning, C.D., et al. "Introduction to Information Retrieval" (2008), Chapter 13
