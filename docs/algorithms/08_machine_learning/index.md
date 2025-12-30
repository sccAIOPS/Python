# Machine Learning Algorithms

This section contains comprehensive documentation for machine learning algorithms implemented in TheAlgorithms/Python repository.

## Categories

### Supervised Learning

#### Regression
| Algorithm | Description | Complexity |
|-----------|-------------|------------|
| [Linear Regression](linear_regression.md) | Normal equation and gradient descent | O(nd²) / O(ndi) |
| [Gradient Descent](gradient_descent.md) | SGD, Adam, RMSprop optimizers | O(ndi) |

#### Classification
| Algorithm | Description | Complexity |
|-----------|-------------|------------|
| [Logistic Regression](logistic_regression.md) | Binary and multiclass classification | O(ndi) |
| [K-Nearest Neighbors](knn.md) | Instance-based lazy learning | O(nd) |
| [Decision Tree](decision_tree.md) | CART, ID3, C4.5 implementations | O(nd log n) |
| [Naive Bayes](naive_bayes.md) | Gaussian, Multinomial, Bernoulli variants | O(nd) |
| [SVM](svm.md) | Support Vector Machines with kernels | O(n²d) |
| [Perceptron](perceptron.md) | Single-layer neural network | O(ndi) |
| [MLP](mlp.md) | Multi-layer Perceptron | O(ndhi) |

#### Ensemble Methods
| Algorithm | Description | Complexity |
|-----------|-------------|------------|
| [Random Forest](random_forest.md) | Bagging ensemble of decision trees | O(Tnd log n) |
| [Boosting](boosting.md) | AdaBoost, Gradient Boosting, XGBoost | O(Tnd log n) |

### Unsupervised Learning

#### Clustering
| Algorithm | Description | Complexity |
|-----------|-------------|------------|
| [K-Means](k_means.md) | Lloyd's algorithm, K-Means++ | O(nkdi) |
| [DBSCAN](dbscan.md) | Density-based spatial clustering | O(n log n) / O(n²) |

#### Dimensionality Reduction
| Algorithm | Description | Complexity |
|-----------|-------------|------------|
| [PCA](pca.md) | Principal Component Analysis | O(d²n) / O(d³) |

## Algorithm Selection Guide

### By Problem Type

```
Classification Problem?
├── Yes
│   ├── Linear decision boundary?
│   │   ├── Yes → Logistic Regression, SVM (linear), Perceptron
│   │   └── No → Decision Tree, Random Forest, SVM (RBF), MLP
│   ├── Need probability estimates?
│   │   ├── Yes → Logistic Regression, Naive Bayes, Random Forest
│   │   └── No → SVM, KNN
│   └── High-dimensional data?
│       ├── Yes → SVM, Naive Bayes, Regularized Logistic
│       └── No → Any classifier
│
├── Regression Problem?
│   ├── Linear relationship?
│   │   ├── Yes → Linear Regression
│   │   └── No → Gradient Boosting, Random Forest, MLP
│   └── Need interpretability?
│       ├── Yes → Linear Regression, Decision Tree
│       └── No → Ensemble methods
│
└── Clustering Problem?
    ├── Know number of clusters?
    │   ├── Yes → K-Means
    │   └── No → DBSCAN, Hierarchical
    ├── Arbitrary cluster shapes?
    │   ├── Yes → DBSCAN
    │   └── No → K-Means
    └── Need to detect outliers?
        ├── Yes → DBSCAN
        └── No → K-Means
```

### By Data Characteristics

| Characteristic | Recommended Algorithms |
|---------------|----------------------|
| Small dataset (< 1K) | SVM, KNN, Any |
| Medium dataset (1K-100K) | Random Forest, Gradient Boosting, MLP |
| Large dataset (> 100K) | Linear models, SGD variants, Mini-batch |
| High dimensions | SVM, Regularized Linear, PCA preprocessing |
| Sparse data | Naive Bayes, Linear SVM, Logistic |
| Imbalanced classes | Random Forest, SVM, SMOTE preprocessing |
| Missing values | Random Forest, Mean imputation + any |
| Categorical features | Decision Tree, Random Forest, Encoding + any |
| Text data | Naive Bayes, SVM, TF-IDF + Linear |
| Time series | ARIMA, LSTM (in neural_network/) |

## Complexity Reference

### Training Time Complexity

| Algorithm | Best | Average | Worst |
|-----------|------|---------|-------|
| Linear Regression (Normal) | O(nd²) | O(nd²) | O(nd²) |
| Linear Regression (GD) | O(ndi) | O(ndi) | O(ndi) |
| Logistic Regression | O(ndi) | O(ndi) | O(ndi) |
| K-Nearest Neighbors | O(1) | O(1) | O(1) |
| Decision Tree | O(nd log n) | O(nd log n) | O(nd × n) |
| Random Forest | O(Tnd log n) | O(Tnd log n) | O(Tnd × n) |
| Gradient Boosting | O(Tnd log n) | O(Tnd log n) | O(Tnd × n) |
| SVM | O(n²d) | O(n²d) | O(n³) |
| Naive Bayes | O(nd) | O(nd) | O(nd) |
| K-Means | O(nkdi) | O(nkdi) | O(nkdi) |
| DBSCAN | O(n log n) | O(n log n) | O(n²) |
| PCA | O(d²n) | O(d²n) | O(d²n + d³) |
| MLP | O(ndhi) | O(ndhi) | O(ndhi) |

Where: n = samples, d = features, k = clusters/neighbors, T = trees, i = iterations, h = hidden units

### Prediction Time Complexity

| Algorithm | Single Sample | Batch (N samples) |
|-----------|--------------|-------------------|
| Linear Regression | O(d) | O(Nd) |
| Logistic Regression | O(d) | O(Nd) |
| KNN | O(nd) / O(d log n) | O(Nnd) / O(Nd log n) |
| Decision Tree | O(log n) | O(N log n) |
| Random Forest | O(T log n) | O(NT log n) |
| Gradient Boosting | O(T log n) | O(NT log n) |
| SVM | O(sv × d) | O(N × sv × d) |
| Naive Bayes | O(d) | O(Nd) |
| MLP | O(dh + hc) | O(N(dh + hc)) |

## Implementation Patterns

### Common Interface

All implementations follow a scikit-learn-like interface:

```python
class Classifier:
    def __init__(self, **params):
        """Initialize hyperparameters."""
        pass
    
    def fit(self, X: List[List[float]], y: List) -> 'Classifier':
        """Train on data."""
        return self
    
    def predict(self, X: List[List[float]]) -> List:
        """Make predictions."""
        pass
    
    def score(self, X: List[List[float]], y: List) -> float:
        """Evaluate accuracy/R² score."""
        pass
```

### Best Practices

1. **Feature Scaling**: Most algorithms benefit from standardization
2. **Cross-Validation**: Always validate on held-out data
3. **Hyperparameter Tuning**: Use grid search or random search
4. **Feature Selection**: Reduce dimensionality when needed
5. **Ensemble Methods**: Combine models for better performance
6. **Regularization**: Prevent overfitting in linear models

## Real-World Applications

| Domain | Algorithm Choice | Reason |
|--------|-----------------|--------|
| Spam Detection | Naive Bayes, SVM | Fast, works with text |
| Credit Scoring | Logistic Regression, GBM | Interpretable, accurate |
| Image Classification | MLP, CNN | Handles complex patterns |
| Customer Segmentation | K-Means, DBSCAN | Unsupervised discovery |
| Fraud Detection | Random Forest, SVM | Handles imbalanced data |
| Recommendation | KNN, Matrix Factorization | Similarity-based |
| Medical Diagnosis | Decision Tree, GBM | Interpretable, accurate |
| Anomaly Detection | DBSCAN, Isolation Forest | Outlier identification |

## Directory Structure

```
08_machine_learning/
├── index.md                 # This file
├── linear_regression.md     # Linear regression methods
├── logistic_regression.md   # Logistic regression
├── knn.md                   # K-Nearest Neighbors
├── k_means.md               # K-Means clustering
├── decision_tree.md         # Decision trees (CART, ID3)
├── naive_bayes.md           # Naive Bayes variants
├── pca.md                   # Principal Component Analysis
├── gradient_descent.md      # Optimization algorithms
├── svm.md                   # Support Vector Machines
├── perceptron.md            # Perceptron algorithm
├── mlp.md                   # Multi-layer Perceptron
├── random_forest.md         # Random Forest ensemble
├── boosting.md              # AdaBoost, Gradient Boosting
└── dbscan.md                # DBSCAN clustering
```

## See Also

- [Neural Network Algorithms](../neural_network/) - Deep learning implementations
- [Data Structures](../05_data_structures/) - Supporting data structures
- [Mathematics](../07_mathematics/) - Mathematical foundations
