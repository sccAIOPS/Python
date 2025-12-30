# Principal Component Analysis (PCA)

## Overview
- **Category**: Unsupervised Learning - Dimensionality Reduction
- **Complexity**: Time: O(d³ + nd²) | Space: O(d²)
- **Type**: Linear transformation, Feature extraction
- **Source File**: [machine_learning/pca.py](../../../machine_learning/pca.py)

## 1. Mathematical Foundation

### 1.1 The Problem

Given data matrix $\mathbf{X} \in \mathbb{R}^{n \times d}$, find lower-dimensional representation $\mathbf{Z} \in \mathbb{R}^{n \times k}$ (where $k < d$) that preserves maximum variance.

### 1.2 Variance Maximization

Find projection direction $\mathbf{w}$ that maximizes variance of projected data:

$$
\text{Var}(\mathbf{X}\mathbf{w}) = \mathbf{w}^T \mathbf{C} \mathbf{w}
$$

where $\mathbf{C}$ is the covariance matrix:
$$
\mathbf{C} = \frac{1}{n-1} \mathbf{X}^T \mathbf{X}
$$

Subject to constraint $\|\mathbf{w}\| = 1$.

### 1.3 Eigenvalue Problem

Using Lagrange multipliers, the solution is an eigenvector of $\mathbf{C}$:

$$
\mathbf{C} \mathbf{w} = \lambda \mathbf{w}
$$

- **Principal components**: Eigenvectors of covariance matrix
- **Explained variance**: Corresponding eigenvalues

### 1.4 Algorithm Steps

1. **Center data**: Subtract mean from each feature
2. **Compute covariance matrix**: $\mathbf{C} = \frac{1}{n-1}\mathbf{X}^T\mathbf{X}$
3. **Eigendecomposition**: Find eigenvectors and eigenvalues
4. **Sort**: Order by decreasing eigenvalue
5. **Project**: $\mathbf{Z} = \mathbf{X}\mathbf{W}_k$ where $\mathbf{W}_k$ contains top $k$ eigenvectors

### 1.5 SVD Approach (More Stable)

Using SVD: $\mathbf{X} = \mathbf{U}\mathbf{\Sigma}\mathbf{V}^T$

- Principal components: Columns of $\mathbf{V}$
- Explained variance: $\sigma_i^2 / (n-1)$
- Projected data: $\mathbf{Z} = \mathbf{U}_k \mathbf{\Sigma}_k$

### 1.6 Explained Variance Ratio

Proportion of variance explained by component $i$:

$$
\rho_i = \frac{\lambda_i}{\sum_{j=1}^{d} \lambda_j}
$$

Cumulative explained variance:
$$
\sum_{i=1}^{k} \rho_i
$$

## 2. Pseudocode

```
ALGORITHM PCA(X, n_components)
    INPUT: Data matrix X (n×d), number of components k
    OUTPUT: Principal components W, projected data Z
    
    // Step 1: Center data
    mean ← column_means(X)
    X_centered ← X - mean
    
    // Step 2: Compute covariance matrix
    C ← (1/(n-1)) × X_centered^T × X_centered
    
    // Step 3: Eigendecomposition
    eigenvalues, eigenvectors ← eigen(C)
    
    // Step 4: Sort by eigenvalue (descending)
    sorted_indices ← argsort(eigenvalues, descending=True)
    eigenvalues ← eigenvalues[sorted_indices]
    eigenvectors ← eigenvectors[:, sorted_indices]
    
    // Step 5: Select top k components
    W ← eigenvectors[:, :k]
    explained_variance ← eigenvalues[:k]
    
    // Step 6: Project data
    Z ← X_centered × W
    
    return W, Z, explained_variance, mean

ALGORITHM PCA_Transform(X_new, W, mean)
    INPUT: New data, components W, original mean
    OUTPUT: Projected new data
    
    X_centered ← X_new - mean
    Z ← X_centered × W
    
    return Z

ALGORITHM PCA_Inverse_Transform(Z, W, mean)
    INPUT: Projected data Z, components W, mean
    OUTPUT: Reconstructed data
    
    X_reconstructed ← Z × W^T + mean
    return X_reconstructed
```

## 3. Step-by-Step Example

### Example: 2D to 1D PCA

**Data**:
| Point | $x_1$ | $x_2$ |
|-------|-------|-------|
| 1 | 2.5 | 2.4 |
| 2 | 0.5 | 0.7 |
| 3 | 2.2 | 2.9 |
| 4 | 1.9 | 2.2 |
| 5 | 3.1 | 3.0 |

**Step 1**: Center data
- Mean: $\bar{x_1} = 2.04$, $\bar{x_2} = 2.24$
- Centered data: subtract means

**Step 2**: Covariance matrix
$$
\mathbf{C} = \begin{pmatrix} 0.616 & 0.615 \\ 0.615 & 0.716 \end{pmatrix}
$$

**Step 3**: Eigendecomposition
- $\lambda_1 = 1.284$, $\mathbf{v}_1 = (0.677, 0.735)$
- $\lambda_2 = 0.049$, $\mathbf{v}_2 = (-0.735, 0.677)$

**Step 4**: Project onto first PC
$$
z_i = (x_{i1} - 2.04) \cdot 0.677 + (x_{i2} - 2.24) \cdot 0.735
$$

**Explained variance**: $1.284 / (1.284 + 0.049) = 96.3\%$

## 4. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Center data | O(nd) | O(nd) |
| Covariance matrix | O(nd²) | O(d²) |
| Eigendecomposition | O(d³) | O(d²) |
| SVD (full) | O(min(nd², n²d)) | O(nd) |
| Transform | O(ndk) | O(nk) |
| Total | O(d³ + nd²) | O(d²) |

For large d: Use SVD or randomized PCA.

## 5. Visual Representation

### 5.1 Principal Components

```
x₂ │        ●
   │      ●   ╱ PC1 (high variance)
   │    ●   ╱
   │  ●   ╱
   │●   ╱
   │  ╱
   │╱____________ PC2 (low variance)
   └──────────────→ x₁
```

### 5.2 Variance Explained

```
Variance │
Ratio    │ ■■■■■■■■■■
         │ ■■■■■■■■■■ 60%
         │ ■■■■■
         │ ■■■■■ 25%
         │ ■■
         │ ■■ 10%
         │ ■ 5%
         └────────────────
           PC1  PC2  PC3  PC4
```

## 6. Implementation

```python
from typing import List, Tuple, Optional
import math


class PCA:
    """
    Principal Component Analysis implementation.
    
    Dimensionality reduction using eigendecomposition.
    """
    
    def __init__(
        self,
        n_components: Optional[int] = None,
        explained_variance_ratio: Optional[float] = None
    ):
        """
        Initialize PCA.
        
        Args:
            n_components: Number of components to keep
            explained_variance_ratio: Minimum variance ratio to explain
        """
        self.n_components = n_components
        self.target_variance_ratio = explained_variance_ratio
        
        self.components: List[List[float]] = []
        self.mean: List[float] = []
        self.explained_variance: List[float] = []
        self.explained_variance_ratio: List[float] = []
        self.n_features: int = 0
        self.n_components_: int = 0
    
    def _center(self, X: List[List[float]]) -> List[List[float]]:
        """Center data by subtracting mean."""
        n = len(X)
        d = len(X[0])
        
        # Compute mean
        self.mean = [0.0] * d
        for i in range(n):
            for j in range(d):
                self.mean[j] += X[i][j]
        self.mean = [m / n for m in self.mean]
        
        # Center
        centered = []
        for i in range(n):
            row = [X[i][j] - self.mean[j] for j in range(d)]
            centered.append(row)
        
        return centered
    
    def _covariance_matrix(
        self, 
        X: List[List[float]]
    ) -> List[List[float]]:
        """Compute covariance matrix."""
        n = len(X)
        d = len(X[0])
        
        cov = [[0.0] * d for _ in range(d)]
        
        for i in range(d):
            for j in range(i, d):
                s = sum(X[k][i] * X[k][j] for k in range(n))
                cov[i][j] = s / (n - 1)
                cov[j][i] = cov[i][j]
        
        return cov
    
    def _power_iteration(
        self,
        matrix: List[List[float]],
        max_iter: int = 1000,
        tol: float = 1e-10
    ) -> Tuple[float, List[float]]:
        """Find dominant eigenvalue/eigenvector using power iteration."""
        d = len(matrix)
        
        # Random initial vector
        import random
        v = [random.gauss(0, 1) for _ in range(d)]
        
        # Normalize
        norm = math.sqrt(sum(x**2 for x in v))
        v = [x / norm for x in v]
        
        eigenvalue = 0.0
        
        for _ in range(max_iter):
            # Matrix-vector multiplication
            v_new = [
                sum(matrix[i][j] * v[j] for j in range(d))
                for i in range(d)
            ]
            
            # Compute eigenvalue (Rayleigh quotient)
            new_eigenvalue = sum(v_new[i] * v[i] for i in range(d))
            
            # Normalize
            norm = math.sqrt(sum(x**2 for x in v_new))
            if norm < 1e-15:
                break
            v_new = [x / norm for x in v_new]
            
            # Check convergence
            if abs(new_eigenvalue - eigenvalue) < tol:
                break
            
            eigenvalue = new_eigenvalue
            v = v_new
        
        return eigenvalue, v
    
    def _deflate(
        self,
        matrix: List[List[float]],
        eigenvalue: float,
        eigenvector: List[float]
    ) -> List[List[float]]:
        """Remove contribution of eigenvector from matrix."""
        d = len(matrix)
        
        deflated = [[0.0] * d for _ in range(d)]
        
        for i in range(d):
            for j in range(d):
                deflated[i][j] = matrix[i][j] - eigenvalue * eigenvector[i] * eigenvector[j]
        
        return deflated
    
    def _eigendecomposition(
        self,
        cov: List[List[float]],
        n_components: int
    ) -> Tuple[List[float], List[List[float]]]:
        """
        Compute eigenvalues and eigenvectors using power iteration.
        """
        d = len(cov)
        eigenvalues = []
        eigenvectors = []
        
        current_matrix = [row.copy() for row in cov]
        
        for _ in range(min(n_components, d)):
            eigenvalue, eigenvector = self._power_iteration(current_matrix)
            
            if eigenvalue < 1e-10:
                break
            
            eigenvalues.append(eigenvalue)
            eigenvectors.append(eigenvector)
            
            # Deflate matrix
            current_matrix = self._deflate(current_matrix, eigenvalue, eigenvector)
        
        return eigenvalues, eigenvectors
    
    def fit(self, X: List[List[float]]) -> 'PCA':
        """
        Fit PCA to data.
        
        >>> pca = PCA(n_components=2)
        >>> X = [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10, 11, 12]]
        >>> pca.fit(X)  # doctest: +ELLIPSIS
        <...PCA object at ...>
        >>> len(pca.components)
        2
        """
        self.n_features = len(X[0])
        
        # Determine number of components
        if self.n_components is not None:
            n_comp = min(self.n_components, self.n_features)
        else:
            n_comp = self.n_features
        
        # Center data
        X_centered = self._center(X)
        
        # Compute covariance matrix
        cov = self._covariance_matrix(X_centered)
        
        # Eigendecomposition
        eigenvalues, eigenvectors = self._eigendecomposition(cov, n_comp)
        
        # Handle variance ratio threshold
        if self.target_variance_ratio is not None:
            total_var = sum(eigenvalues)
            cumsum = 0
            n_comp = 0
            for ev in eigenvalues:
                cumsum += ev
                n_comp += 1
                if cumsum / total_var >= self.target_variance_ratio:
                    break
            eigenvalues = eigenvalues[:n_comp]
            eigenvectors = eigenvectors[:n_comp]
        
        self.n_components_ = len(eigenvectors)
        self.components = eigenvectors
        self.explained_variance = eigenvalues
        
        # Compute explained variance ratio
        total = sum(eigenvalues) if eigenvalues else 1
        self.explained_variance_ratio = [ev / total for ev in eigenvalues]
        
        return self
    
    def transform(self, X: List[List[float]]) -> List[List[float]]:
        """
        Transform data to principal component space.
        
        >>> pca = PCA(n_components=1)
        >>> X = [[1, 2], [3, 4], [5, 6]]
        >>> pca.fit(X)  # doctest: +ELLIPSIS
        <...>
        >>> Z = pca.transform([[2, 3], [4, 5]])
        >>> len(Z[0])
        1
        """
        result = []
        
        for x in X:
            # Center
            x_centered = [x[j] - self.mean[j] for j in range(self.n_features)]
            
            # Project onto components
            z = []
            for component in self.components:
                proj = sum(x_centered[j] * component[j] for j in range(self.n_features))
                z.append(proj)
            
            result.append(z)
        
        return result
    
    def fit_transform(self, X: List[List[float]]) -> List[List[float]]:
        """Fit and transform in one step."""
        self.fit(X)
        return self.transform(X)
    
    def inverse_transform(self, Z: List[List[float]]) -> List[List[float]]:
        """
        Transform data back to original space.
        
        >>> pca = PCA(n_components=1)
        >>> X = [[1, 2], [3, 4], [5, 6]]
        >>> pca.fit(X)  # doctest: +ELLIPSIS
        <...>
        >>> Z = pca.transform(X)
        >>> X_reconstructed = pca.inverse_transform(Z)
        >>> len(X_reconstructed)
        3
        """
        result = []
        
        for z in Z:
            # Reconstruct in original space
            x_reconstructed = [0.0] * self.n_features
            
            for i, component in enumerate(self.components):
                for j in range(self.n_features):
                    x_reconstructed[j] += z[i] * component[j]
            
            # Add mean back
            for j in range(self.n_features):
                x_reconstructed[j] += self.mean[j]
            
            result.append(x_reconstructed)
        
        return result
    
    def reconstruction_error(self, X: List[List[float]]) -> float:
        """Compute mean squared reconstruction error."""
        Z = self.transform(X)
        X_reconstructed = self.inverse_transform(Z)
        
        total_error = 0.0
        n = len(X)
        d = len(X[0])
        
        for i in range(n):
            for j in range(d):
                total_error += (X[i][j] - X_reconstructed[i][j]) ** 2
        
        return total_error / (n * d)
    
    def cumulative_explained_variance(self) -> List[float]:
        """Compute cumulative explained variance ratio."""
        cumsum = []
        total = 0.0
        for ratio in self.explained_variance_ratio:
            total += ratio
            cumsum.append(total)
        return cumsum


class IncrementalPCA:
    """
    Incremental PCA for large datasets.
    
    Processes data in batches using SVD updates.
    """
    
    def __init__(self, n_components: int = 2, batch_size: int = 100):
        self.n_components = n_components
        self.batch_size = batch_size
        
        self.components: List[List[float]] = []
        self.mean: List[float] = []
        self.var: List[float] = []
        self.n_samples_seen: int = 0
        self.singular_values: List[float] = []
    
    def partial_fit(self, X: List[List[float]]) -> 'IncrementalPCA':
        """
        Incrementally fit PCA on batch.
        
        >>> ipca = IncrementalPCA(n_components=2)
        >>> ipca.partial_fit([[1, 2, 3], [4, 5, 6]])  # doctest: +ELLIPSIS
        <...IncrementalPCA object at ...>
        >>> ipca.partial_fit([[7, 8, 9], [10, 11, 12]])  # doctest: +ELLIPSIS
        <...>
        """
        n = len(X)
        d = len(X[0])
        
        if self.n_samples_seen == 0:
            # First batch - initialize
            self.mean = [0.0] * d
            for row in X:
                for j in range(d):
                    self.mean[j] += row[j]
            self.mean = [m / n for m in self.mean]
        else:
            # Update running mean
            new_mean = [0.0] * d
            for row in X:
                for j in range(d):
                    new_mean[j] += row[j]
            new_mean = [m / n for m in new_mean]
            
            # Weighted update
            total = self.n_samples_seen + n
            for j in range(d):
                self.mean[j] = (
                    self.n_samples_seen * self.mean[j] + n * new_mean[j]
                ) / total
        
        # Center current batch
        X_centered = []
        for row in X:
            centered_row = [row[j] - self.mean[j] for j in range(d)]
            X_centered.append(centered_row)
        
        # Simple PCA on batch (for demonstration)
        pca = PCA(n_components=self.n_components)
        pca.fit(X_centered)
        
        if self.components:
            # Combine with existing components
            # (Simplified - full incremental SVD is more complex)
            for i in range(min(len(pca.components), len(self.components))):
                for j in range(d):
                    self.components[i][j] = (
                        self.components[i][j] + pca.components[i][j]
                    ) / 2
        else:
            self.components = pca.components
        
        self.n_samples_seen += n
        
        return self
    
    def transform(self, X: List[List[float]]) -> List[List[float]]:
        """Transform data using learned components."""
        result = []
        d = len(self.mean)
        
        for x in X:
            x_centered = [x[j] - self.mean[j] for j in range(d)]
            
            z = []
            for component in self.components:
                proj = sum(x_centered[j] * component[j] for j in range(d))
                z.append(proj)
            
            result.append(z)
        
        return result


class KernelPCA:
    """
    Kernel PCA for non-linear dimensionality reduction.
    """
    
    def __init__(
        self,
        n_components: int = 2,
        kernel: str = 'rbf',
        gamma: float = 1.0
    ):
        """
        Initialize Kernel PCA.
        
        Args:
            n_components: Number of components
            kernel: 'rbf', 'poly', or 'linear'
            gamma: Kernel parameter
        """
        self.n_components = n_components
        self.kernel = kernel
        self.gamma = gamma
        
        self.X_fit: List[List[float]] = []
        self.alphas: List[List[float]] = []
        self.lambdas: List[float] = []
    
    def _kernel_function(
        self,
        x1: List[float],
        x2: List[float]
    ) -> float:
        """Compute kernel value."""
        if self.kernel == 'linear':
            return sum(a * b for a, b in zip(x1, x2))
        
        elif self.kernel == 'rbf':
            sq_dist = sum((a - b) ** 2 for a, b in zip(x1, x2))
            return math.exp(-self.gamma * sq_dist)
        
        elif self.kernel == 'poly':
            dot = sum(a * b for a, b in zip(x1, x2))
            return (dot + 1) ** 2
        
        else:
            raise ValueError(f"Unknown kernel: {self.kernel}")
    
    def _kernel_matrix(self, X: List[List[float]]) -> List[List[float]]:
        """Compute kernel matrix."""
        n = len(X)
        K = [[0.0] * n for _ in range(n)]
        
        for i in range(n):
            for j in range(i, n):
                k_val = self._kernel_function(X[i], X[j])
                K[i][j] = k_val
                K[j][i] = k_val
        
        return K
    
    def _center_kernel_matrix(
        self,
        K: List[List[float]]
    ) -> List[List[float]]:
        """Center kernel matrix in feature space."""
        n = len(K)
        
        # Row means
        row_means = [sum(K[i]) / n for i in range(n)]
        
        # Total mean
        total_mean = sum(row_means) / n
        
        # Centered kernel matrix
        K_centered = [[0.0] * n for _ in range(n)]
        
        for i in range(n):
            for j in range(n):
                K_centered[i][j] = (
                    K[i][j] - row_means[i] - row_means[j] + total_mean
                )
        
        return K_centered
    
    def fit(self, X: List[List[float]]) -> 'KernelPCA':
        """
        Fit Kernel PCA.
        
        >>> kpca = KernelPCA(n_components=2, kernel='rbf', gamma=0.5)
        >>> X = [[1, 2], [2, 3], [3, 4], [10, 11], [11, 12]]
        >>> kpca.fit(X)  # doctest: +ELLIPSIS
        <...KernelPCA object at ...>
        """
        self.X_fit = X
        
        # Compute and center kernel matrix
        K = self._kernel_matrix(X)
        K_centered = self._center_kernel_matrix(K)
        
        # Eigendecomposition (using power iteration for simplicity)
        n = len(K_centered)
        pca = PCA(n_components=self.n_components)
        
        # Treat kernel matrix as covariance-like
        # (simplified - proper implementation uses eigendecomposition of K)
        lambdas = []
        alphas = []
        
        current_K = [row.copy() for row in K_centered]
        
        for _ in range(self.n_components):
            eigenvalue, eigenvector = pca._power_iteration(current_K)
            
            if eigenvalue < 1e-10:
                break
            
            lambdas.append(eigenvalue)
            
            # Normalize alpha
            norm = math.sqrt(eigenvalue)
            alpha = [v / norm if norm > 0 else v for v in eigenvector]
            alphas.append(alpha)
            
            current_K = pca._deflate(current_K, eigenvalue, eigenvector)
        
        self.lambdas = lambdas
        self.alphas = alphas
        
        return self
    
    def transform(self, X: List[List[float]]) -> List[List[float]]:
        """Transform data using kernel PCA."""
        result = []
        n_train = len(self.X_fit)
        
        for x in X:
            # Compute kernel with training data
            k_vec = [self._kernel_function(x, self.X_fit[i]) for i in range(n_train)]
            
            # Project onto components
            z = []
            for alpha in self.alphas:
                proj = sum(k_vec[i] * alpha[i] for i in range(n_train))
                z.append(proj)
            
            result.append(z)
        
        return result
```

## 7. Applications

### 7.1 Classic Applications

- **Data visualization**: Reduce to 2D/3D for plotting
- **Noise reduction**: Remove low-variance components
- **Feature extraction**: Create uncorrelated features
- **Compression**: Reduce storage/bandwidth
- **Preprocessing**: For other ML algorithms

### 7.2 When to Use PCA

| Scenario | Suitability |
|----------|-------------|
| High-dimensional data | ✅ Excellent |
| Visualization | ✅ Excellent |
| Preprocessing | ✅ Good |
| Non-linear relationships | ❌ Use Kernel PCA |
| Categorical features | ❌ Use MCA |

## 8. Real-World Software Engineering Applications

### 8.1 Production Example: Image Compression

```python
from typing import List, Tuple, Optional
import math


class ImageCompressor:
    """
    Image compression using PCA.
    
    Reduces image dimensionality while preserving key features.
    """
    
    def __init__(self, variance_ratio: float = 0.95):
        """
        Initialize compressor.
        
        Args:
            variance_ratio: Target explained variance ratio
        """
        self.variance_ratio = variance_ratio
        self.pca_r = PCA()
        self.pca_g = PCA()
        self.pca_b = PCA()
        self.original_shape: Tuple[int, int] = (0, 0)
    
    def compress(
        self,
        image: List[List[Tuple[int, int, int]]]
    ) -> dict:
        """
        Compress image using PCA.
        
        Args:
            image: 2D array of RGB tuples
        
        Returns:
            Compressed representation
        """
        height = len(image)
        width = len(image[0])
        self.original_shape = (height, width)
        
        # Separate channels
        R = [[float(image[i][j][0]) for j in range(width)] for i in range(height)]
        G = [[float(image[i][j][1]) for j in range(width)] for i in range(height)]
        B = [[float(image[i][j][2]) for j in range(width)] for i in range(height)]
        
        # Fit PCA for each channel
        self.pca_r = PCA(explained_variance_ratio=self.variance_ratio)
        self.pca_g = PCA(explained_variance_ratio=self.variance_ratio)
        self.pca_b = PCA(explained_variance_ratio=self.variance_ratio)
        
        Z_r = self.pca_r.fit_transform(R)
        Z_g = self.pca_g.fit_transform(G)
        Z_b = self.pca_b.fit_transform(B)
        
        # Compression statistics
        original_size = height * width * 3
        compressed_size = (
            len(Z_r) * len(Z_r[0]) +
            len(Z_g) * len(Z_g[0]) +
            len(Z_b) * len(Z_b[0])
        )
        # Add component storage
        compressed_size += (
            len(self.pca_r.components) * width +
            len(self.pca_g.components) * width +
            len(self.pca_b.components) * width
        )
        
        return {
            'compressed': {'R': Z_r, 'G': Z_g, 'B': Z_b},
            'n_components': {
                'R': self.pca_r.n_components_,
                'G': self.pca_g.n_components_,
                'B': self.pca_b.n_components_
            },
            'compression_ratio': original_size / compressed_size,
            'explained_variance': {
                'R': sum(self.pca_r.explained_variance_ratio),
                'G': sum(self.pca_g.explained_variance_ratio),
                'B': sum(self.pca_b.explained_variance_ratio)
            }
        }
    
    def decompress(
        self,
        compressed: dict
    ) -> List[List[Tuple[int, int, int]]]:
        """
        Decompress image from PCA representation.
        """
        Z_r = compressed['compressed']['R']
        Z_g = compressed['compressed']['G']
        Z_b = compressed['compressed']['B']
        
        R = self.pca_r.inverse_transform(Z_r)
        G = self.pca_g.inverse_transform(Z_g)
        B = self.pca_b.inverse_transform(Z_b)
        
        height, width = self.original_shape
        image = []
        
        for i in range(height):
            row = []
            for j in range(width):
                # Clamp to valid range
                r = max(0, min(255, int(R[i][j])))
                g = max(0, min(255, int(G[i][j])))
                b = max(0, min(255, int(B[i][j])))
                row.append((r, g, b))
            image.append(row)
        
        return image


class FeatureExtractor:
    """
    Feature extraction for machine learning pipelines.
    """
    
    def __init__(
        self,
        n_components: Optional[int] = None,
        variance_threshold: float = 0.95
    ):
        """
        Initialize feature extractor.
        
        Args:
            n_components: Fixed number of components (overrides variance)
            variance_threshold: Minimum variance to explain
        """
        self.n_components = n_components
        self.variance_threshold = variance_threshold
        
        if n_components is not None:
            self.pca = PCA(n_components=n_components)
        else:
            self.pca = PCA(explained_variance_ratio=variance_threshold)
        
        self.feature_means: List[float] = []
        self.feature_stds: List[float] = []
    
    def _standardize(
        self,
        X: List[List[float]],
        fit: bool = True
    ) -> List[List[float]]:
        """Standardize features (z-score normalization)."""
        n = len(X)
        d = len(X[0])
        
        if fit:
            self.feature_means = []
            self.feature_stds = []
            
            for j in range(d):
                values = [X[i][j] for i in range(n)]
                mean = sum(values) / n
                variance = sum((v - mean) ** 2 for v in values) / n
                std = math.sqrt(variance) if variance > 0 else 1.0
                
                self.feature_means.append(mean)
                self.feature_stds.append(std)
        
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
        X: List[List[float]],
        feature_names: Optional[List[str]] = None
    ) -> 'FeatureExtractor':
        """
        Fit feature extractor.
        
        >>> fe = FeatureExtractor(n_components=2)
        >>> X = [[1, 2, 3, 4], [5, 6, 7, 8], [9, 10, 11, 12]]
        >>> fe.fit(X)  # doctest: +ELLIPSIS
        <...FeatureExtractor object at ...>
        """
        X_standardized = self._standardize(X, fit=True)
        self.pca.fit(X_standardized)
        
        return self
    
    def transform(self, X: List[List[float]]) -> List[List[float]]:
        """Transform features to principal components."""
        X_standardized = self._standardize(X, fit=False)
        return self.pca.transform(X_standardized)
    
    def fit_transform(self, X: List[List[float]]) -> List[List[float]]:
        """Fit and transform in one step."""
        self.fit(X)
        return self.transform(X)
    
    def get_component_importance(
        self,
        feature_names: Optional[List[str]] = None
    ) -> List[List[Tuple[str, float]]]:
        """
        Get feature importance for each principal component.
        
        Returns sorted list of (feature_name, loading) for each PC.
        """
        d = len(self.pca.components[0])
        
        if feature_names is None:
            feature_names = [f"feature_{i}" for i in range(d)]
        
        importance = []
        
        for i, component in enumerate(self.pca.components):
            loadings = [
                (feature_names[j], abs(component[j]))
                for j in range(d)
            ]
            loadings.sort(key=lambda x: x[1], reverse=True)
            importance.append(loadings)
        
        return importance
    
    def report(self) -> str:
        """Generate report on dimensionality reduction."""
        lines = [
            "PCA Feature Extraction Report",
            "=" * 40,
            f"Original features: {len(self.pca.components[0])}",
            f"Extracted components: {self.pca.n_components_}",
            "",
            "Explained Variance Ratio:",
        ]
        
        cumulative = 0.0
        for i, ratio in enumerate(self.pca.explained_variance_ratio):
            cumulative += ratio
            lines.append(f"  PC{i+1}: {ratio:.2%} (cumulative: {cumulative:.2%})")
        
        return "\n".join(lines)


class AnomalyDetector:
    """
    Anomaly detection using PCA reconstruction error.
    """
    
    def __init__(
        self,
        n_components: int = 5,
        threshold_percentile: float = 95
    ):
        """
        Initialize anomaly detector.
        
        Args:
            n_components: Number of PCA components
            threshold_percentile: Percentile for anomaly threshold
        """
        self.n_components = n_components
        self.threshold_percentile = threshold_percentile
        
        self.pca = PCA(n_components=n_components)
        self.threshold: float = 0.0
        self.mean: List[float] = []
        self.std: List[float] = []
    
    def fit(self, X: List[List[float]]) -> 'AnomalyDetector':
        """
        Fit detector on normal data.
        
        >>> ad = AnomalyDetector(n_components=2)
        >>> X = [[1, 2], [2, 3], [3, 4], [2, 2], [3, 3]]
        >>> ad.fit(X)  # doctest: +ELLIPSIS
        <...AnomalyDetector object at ...>
        """
        n = len(X)
        d = len(X[0])
        
        # Standardize
        self.mean = [0.0] * d
        self.std = [1.0] * d
        
        for j in range(d):
            values = [X[i][j] for i in range(n)]
            self.mean[j] = sum(values) / n
            variance = sum((v - self.mean[j]) ** 2 for v in values) / n
            self.std[j] = math.sqrt(variance) if variance > 0 else 1.0
        
        X_scaled = [
            [(X[i][j] - self.mean[j]) / self.std[j] for j in range(d)]
            for i in range(n)
        ]
        
        self.pca.fit(X_scaled)
        
        # Compute reconstruction errors
        errors = []
        Z = self.pca.transform(X_scaled)
        X_reconstructed = self.pca.inverse_transform(Z)
        
        for i in range(n):
            error = sum((X_scaled[i][j] - X_reconstructed[i][j]) ** 2 for j in range(d))
            errors.append(math.sqrt(error))
        
        # Set threshold at percentile
        errors.sort()
        idx = int(self.threshold_percentile / 100 * n)
        self.threshold = errors[min(idx, n - 1)]
        
        return self
    
    def predict(
        self,
        X: List[List[float]]
    ) -> List[Tuple[bool, float]]:
        """
        Predict anomalies.
        
        Returns list of (is_anomaly, reconstruction_error).
        """
        d = len(self.mean)
        
        X_scaled = [
            [(x[j] - self.mean[j]) / self.std[j] for j in range(d)]
            for x in X
        ]
        
        Z = self.pca.transform(X_scaled)
        X_reconstructed = self.pca.inverse_transform(Z)
        
        results = []
        for i in range(len(X)):
            error = sum(
                (X_scaled[i][j] - X_reconstructed[i][j]) ** 2 
                for j in range(d)
            )
            error = math.sqrt(error)
            
            is_anomaly = error > self.threshold
            results.append((is_anomaly, error))
        
        return results
    
    def score(self, X: List[List[float]]) -> List[float]:
        """Return anomaly scores (reconstruction errors)."""
        return [error for _, error in self.predict(X)]


# Demo
if __name__ == "__main__":
    import random
    random.seed(42)
    
    print("PCA Feature Extraction Demo")
    print("=" * 50)
    
    # Generate correlated data
    n_samples = 100
    n_features = 10
    
    # Create some underlying factors
    factor1 = [random.gauss(0, 1) for _ in range(n_samples)]
    factor2 = [random.gauss(0, 1) for _ in range(n_samples)]
    
    # Generate features as combinations of factors + noise
    X = []
    for i in range(n_samples):
        row = [
            factor1[i] * 0.8 + random.gauss(0, 0.2),
            factor1[i] * 0.7 + random.gauss(0, 0.3),
            factor1[i] * 0.6 + random.gauss(0, 0.4),
            factor2[i] * 0.9 + random.gauss(0, 0.1),
            factor2[i] * 0.8 + random.gauss(0, 0.2),
            factor1[i] * 0.3 + factor2[i] * 0.5 + random.gauss(0, 0.3),
            random.gauss(0, 1),  # Pure noise
            random.gauss(0, 1),
            random.gauss(0, 1),
            random.gauss(0, 1)
        ]
        X.append(row)
    
    # Extract features
    fe = FeatureExtractor(variance_threshold=0.9)
    X_reduced = fe.fit_transform(X)
    
    print(fe.report())
    print(f"\nReduced from {n_features} to {fe.pca.n_components_} features")
    
    print("\n" + "=" * 50)
    print("Anomaly Detection Demo")
    print("=" * 50)
    
    # Normal data
    normal_data = [
        [random.gauss(5, 1), random.gauss(5, 1)]
        for _ in range(100)
    ]
    
    # Anomalies
    anomalies = [
        [15, 15],
        [-5, -5],
        [10, 0]
    ]
    
    # Fit on normal data
    detector = AnomalyDetector(n_components=1, threshold_percentile=95)
    detector.fit(normal_data)
    
    # Test
    test_data = normal_data[:5] + anomalies
    results = detector.predict(test_data)
    
    print("\nAnomaly Detection Results:")
    for i, (is_anomaly, error) in enumerate(results):
        status = "ANOMALY" if is_anomaly else "NORMAL"
        data_type = "Normal" if i < 5 else "Injected anomaly"
        print(f"  {data_type}: {status} (error: {error:.3f})")
    
    print(f"\nThreshold: {detector.threshold:.3f}")
```

## 9. Comparison with Other Methods

| Method | Type | Linearity | Preserves |
|--------|------|-----------|-----------|
| PCA | Unsupervised | Linear | Variance |
| LDA | Supervised | Linear | Class separation |
| t-SNE | Unsupervised | Non-linear | Local structure |
| UMAP | Unsupervised | Non-linear | Global + local |
| Kernel PCA | Unsupervised | Non-linear | Kernel variance |

## 10. Edge Cases and Handling

| Issue | Solution |
|-------|----------|
| Zero variance features | Remove before PCA |
| Different scales | Standardize first |
| Missing values | Imputation |
| Very large d | Randomized PCA |
| Very large n | Incremental PCA |

## 11. Best Practices

1. **Always center** (and usually standardize) data
2. **Check explained variance** ratio to choose components
3. **Use scree plot** / elbow method for component selection
4. **Validate** with downstream task performance
5. **Consider alternatives** for non-linear data

## 12. References

- [Wikipedia: Principal Component Analysis](https://en.wikipedia.org/wiki/Principal_component_analysis)
- Jolliffe, I.T. "Principal Component Analysis" (2002)
- Shlens, J. "A Tutorial on Principal Component Analysis" (2014)
