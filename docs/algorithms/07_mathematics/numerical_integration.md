# Numerical Integration

## Overview
- **Category**: Numerical Methods
- **Complexity**: Time: O(n) | Space: O(1)
- **Type**: Approximation algorithm
- **Source Files**: [maths/numerical_integration.py](../../../maths/numerical_integration.py), [maths/simpson_rule.py](../../../maths/simpson_rule.py)

## 1. Mathematical Foundation

### 1.1 The Problem

Approximate the definite integral:
$$
I = \int_a^b f(x) \, dx
$$

when no closed-form antiderivative exists.

### 1.2 Riemann Sum Interpretation

The integral is the limit of Riemann sums:
$$
\int_a^b f(x) \, dx = \lim_{n \to \infty} \sum_{i=1}^{n} f(x_i^*) \Delta x
$$

where $\Delta x = \frac{b-a}{n}$ and $x_i^*$ is a sample point.

## 2. Methods

### 2.1 Rectangular Rule (Left/Right)

**Left endpoint:**
$$
\int_a^b f(x) \, dx \approx h \sum_{i=0}^{n-1} f(x_i)
$$

**Right endpoint:**
$$
\int_a^b f(x) \, dx \approx h \sum_{i=1}^{n} f(x_i)
$$

**Error**: $O(h)$ - First order

### 2.2 Midpoint Rule

$$
\int_a^b f(x) \, dx \approx h \sum_{i=0}^{n-1} f\left(x_i + \frac{h}{2}\right)
$$

**Error**: $O(h^2)$ - Second order

### 2.3 Trapezoidal Rule

$$
\int_a^b f(x) \, dx \approx \frac{h}{2} \left[f(a) + 2\sum_{i=1}^{n-1} f(x_i) + f(b)\right]
$$

**Error**: $O(h^2)$ - Second order

**Error bound:**
$$
|E_T| \leq \frac{(b-a)^3}{12n^2} \max_{x \in [a,b]} |f''(x)|
$$

### 2.4 Simpson's Rule

Uses quadratic interpolation:
$$
\int_a^b f(x) \, dx \approx \frac{h}{3} \left[f(a) + 4\sum_{i=1,3,5...}^{n-1} f(x_i) + 2\sum_{i=2,4,6...}^{n-2} f(x_i) + f(b)\right]
$$

**Error**: $O(h^4)$ - Fourth order

**Error bound:**
$$
|E_S| \leq \frac{(b-a)^5}{180n^4} \max_{x \in [a,b]} |f^{(4)}(x)|
$$

### 2.5 Simpson's 3/8 Rule

Uses cubic interpolation:
$$
\int_a^b f(x) \, dx \approx \frac{3h}{8} \left[f(x_0) + 3f(x_1) + 3f(x_2) + 2f(x_3) + ... + f(x_n)\right]
$$

**Error**: $O(h^4)$ - Fourth order

### 2.6 Boole's Rule

Fifth-degree polynomial:
$$
\int_a^b f(x) \, dx \approx \frac{2h}{45}\left[7f_0 + 32f_1 + 12f_2 + 32f_3 + 7f_4\right]
$$

**Error**: $O(h^6)$ - Sixth order

## 3. Comparison Table

| Method | Order | Weights | Best For |
|--------|-------|---------|----------|
| Rectangle | 1 | [1,1,...,1] | Quick estimate |
| Trapezoid | 2 | [1,2,...,2,1]/2 | General use |
| Simpson | 4 | [1,4,2,4,...,1]/3 | Smooth functions |
| Simpson 3/8 | 4 | [1,3,3,2,...,1]×3/8 | Cubic data |
| Boole | 6 | [7,32,12,32,7]/45 | High accuracy |

## 4. Pseudocode

```
ALGORITHM TrapezoidalRule(f, a, b, n)
    INPUT: Function f, interval [a,b], n subintervals
    OUTPUT: Approximate integral
    
    h ← (b - a) / n
    sum ← (f(a) + f(b)) / 2
    
    for i ← 1 to n-1 do
        x ← a + i × h
        sum ← sum + f(x)
    
    return h × sum

ALGORITHM SimpsonsRule(f, a, b, n)
    INPUT: Function f, interval [a,b], n subintervals (n must be even)
    OUTPUT: Approximate integral
    
    if n is odd then
        n ← n + 1  // Make even
    
    h ← (b - a) / n
    sum ← f(a) + f(b)
    
    for i ← 1 to n-1 do
        x ← a + i × h
        if i is odd then
            sum ← sum + 4 × f(x)
        else
            sum ← sum + 2 × f(x)
    
    return (h / 3) × sum

ALGORITHM AdaptiveSimpsons(f, a, b, tol)
    INPUT: Function f, interval [a,b], tolerance tol
    OUTPUT: Approximate integral within tolerance
    
    c ← (a + b) / 2
    S_ab ← Simpson(f, a, b)
    S_ac ← Simpson(f, a, c)
    S_cb ← Simpson(f, c, b)
    
    if |S_ac + S_cb - S_ab| < 15 × tol then
        return S_ac + S_cb + (S_ac + S_cb - S_ab) / 15
    else
        left ← AdaptiveSimpsons(f, a, c, tol/2)
        right ← AdaptiveSimpsons(f, c, b, tol/2)
        return left + right
```

## 5. Step-by-Step Example

### Example: $\int_0^1 e^{-x^2} dx$ with n=4

**Trapezoidal Rule:**
- $h = 0.25$
- Points: 0, 0.25, 0.5, 0.75, 1
- Values: 1, 0.9394, 0.7788, 0.5698, 0.3679

$$
I \approx \frac{0.25}{2}[1 + 2(0.9394 + 0.7788 + 0.5698) + 0.3679]
$$
$$
= 0.125 × [1 + 4.576 + 0.3679] = 0.7430
$$

**Simpson's Rule (n=4):**
$$
I \approx \frac{0.25}{3}[1 + 4(0.9394) + 2(0.7788) + 4(0.5698) + 0.3679]
$$
$$
= \frac{0.25}{3}[1 + 3.758 + 1.558 + 2.279 + 0.368] = 0.7469
$$

**Actual value**: $\approx 0.7468$ (Simpson is very accurate!)

## 6. Complexity Analysis

| Method | Evaluations | Memory | Convergence |
|--------|-------------|--------|-------------|
| Rectangle | n | O(1) | O(1/n) |
| Trapezoid | n+1 | O(1) | O(1/n²) |
| Simpson | n+1 | O(1) | O(1/n⁴) |
| Adaptive | variable | O(log n) | guaranteed |

## 7. Visual Representation

### 7.1 Method Comparison

```
         Rectangular          Trapezoidal           Simpson's
         
    f(x) |  ▄▄▄               f(x) |  /\              f(x) |  ~curve~
         | ▄▄▄▄▄                   | /  \                  |  fitted
         |▄▄▄▄▄▄▄                  |/    \                 | parabola
    ─────┴────────           ─────┴──────           ─────┴──────
           x                       x                      x
           
    Underestimate           Linear fit             Quadratic fit
```

### 7.2 Error Convergence

```
Error
  │
  │  *  Rectangle O(h)
  │   * 
  │    *  Trapezoid O(h²)
  │     *
  │       *  Simpson O(h⁴)
  │          *
  │              *
  └─────────────────── n (subdivisions)
```

## 8. Implementation

```python
from typing import Callable, Tuple, List
import math


def rectangular_left(
    f: Callable[[float], float],
    a: float,
    b: float,
    n: int
) -> float:
    """
    Left Riemann sum integration.
    
    >>> rectangular_left(lambda x: x**2, 0, 1, 1000)
    0.3328335
    """
    h = (b - a) / n
    return h * sum(f(a + i * h) for i in range(n))


def rectangular_right(
    f: Callable[[float], float],
    a: float,
    b: float,
    n: int
) -> float:
    """
    Right Riemann sum integration.
    
    >>> rectangular_right(lambda x: x**2, 0, 1, 1000)
    0.3338335
    """
    h = (b - a) / n
    return h * sum(f(a + i * h) for i in range(1, n + 1))


def midpoint(
    f: Callable[[float], float],
    a: float,
    b: float,
    n: int
) -> float:
    """
    Midpoint rule integration.
    
    >>> midpoint(lambda x: x**2, 0, 1, 1000)
    0.33333325
    """
    h = (b - a) / n
    return h * sum(f(a + (i + 0.5) * h) for i in range(n))


def trapezoidal(
    f: Callable[[float], float],
    a: float,
    b: float,
    n: int
) -> float:
    """
    Trapezoidal rule integration.
    
    Error: O(h²) where h = (b-a)/n
    
    >>> trapezoidal(lambda x: x**2, 0, 1, 1000)
    0.33333350
    >>> trapezoidal(math.sin, 0, math.pi, 100)
    1.9998355...
    """
    h = (b - a) / n
    result = (f(a) + f(b)) / 2
    
    for i in range(1, n):
        result += f(a + i * h)
    
    return h * result


def simpson(
    f: Callable[[float], float],
    a: float,
    b: float,
    n: int = 100
) -> float:
    """
    Simpson's 1/3 rule integration.
    
    Requires n to be even. Error: O(h⁴)
    
    >>> simpson(lambda x: x**2, 0, 1, 100)
    0.33333333...
    >>> simpson(math.sin, 0, math.pi, 100)
    2.0000000...
    """
    if n % 2 == 1:
        n += 1  # Make even
    
    h = (b - a) / n
    result = f(a) + f(b)
    
    for i in range(1, n):
        x = a + i * h
        if i % 2 == 1:
            result += 4 * f(x)
        else:
            result += 2 * f(x)
    
    return (h / 3) * result


def simpson_38(
    f: Callable[[float], float],
    a: float,
    b: float,
    n: int = 99
) -> float:
    """
    Simpson's 3/8 rule integration.
    
    Requires n to be multiple of 3.
    
    >>> simpson_38(lambda x: x**2, 0, 1, 99)
    0.33333333...
    """
    while n % 3 != 0:
        n += 1
    
    h = (b - a) / n
    result = f(a) + f(b)
    
    for i in range(1, n):
        x = a + i * h
        if i % 3 == 0:
            result += 2 * f(x)
        else:
            result += 3 * f(x)
    
    return (3 * h / 8) * result


def boole(
    f: Callable[[float], float],
    a: float,
    b: float,
    n: int = 100
) -> float:
    """
    Boole's rule integration.
    
    Requires n to be multiple of 4. Error: O(h⁶)
    
    >>> boole(lambda x: x**2, 0, 1, 100)
    0.33333333...
    """
    while n % 4 != 0:
        n += 1
    
    h = (b - a) / n
    result = 7 * (f(a) + f(b))
    
    for i in range(1, n):
        x = a + i * h
        if i % 4 == 0:
            result += 14 * f(x)
        elif i % 2 == 0:
            result += 12 * f(x)
        else:
            result += 32 * f(x)
    
    return (2 * h / 45) * result


def adaptive_simpson(
    f: Callable[[float], float],
    a: float,
    b: float,
    tol: float = 1e-10,
    max_depth: int = 50
) -> float:
    """
    Adaptive Simpson's rule with error control.
    
    Recursively subdivides until error < tolerance.
    
    >>> adaptive_simpson(lambda x: x**2, 0, 1)
    0.33333333333333337
    """
    def _simpson_basic(a: float, b: float) -> float:
        c = (a + b) / 2
        h = (b - a) / 6
        return h * (f(a) + 4 * f(c) + f(b))
    
    def _adaptive(a: float, b: float, S: float, tol: float, depth: int) -> float:
        c = (a + b) / 2
        S_left = _simpson_basic(a, c)
        S_right = _simpson_basic(c, b)
        
        error = (S_left + S_right - S) / 15
        
        if abs(error) < tol or depth >= max_depth:
            return S_left + S_right + error
        
        return (_adaptive(a, c, S_left, tol/2, depth+1) +
                _adaptive(c, b, S_right, tol/2, depth+1))
    
    S = _simpson_basic(a, b)
    return _adaptive(a, b, S, tol, 0)


def romberg(
    f: Callable[[float], float],
    a: float,
    b: float,
    max_iter: int = 20,
    tol: float = 1e-12
) -> Tuple[float, int]:
    """
    Romberg integration using Richardson extrapolation.
    
    Returns (integral, iterations).
    
    >>> result, iters = romberg(lambda x: x**2, 0, 1)
    >>> abs(result - 1/3) < 1e-12
    True
    """
    R = [[0] * (max_iter + 1) for _ in range(max_iter + 1)]
    
    # R[0,0] = Trapezoidal with n=1
    h = b - a
    R[0][0] = h * (f(a) + f(b)) / 2
    
    for i in range(1, max_iter + 1):
        # Compute R[i,0] using trapezoidal with 2^i intervals
        h = h / 2
        n = 2 ** i
        
        # Add midpoints
        sum_new = sum(f(a + (2*k - 1) * h) for k in range(1, n // 2 + 1))
        R[i][0] = R[i-1][0] / 2 + h * sum_new
        
        # Richardson extrapolation
        for j in range(1, i + 1):
            factor = 4 ** j
            R[i][j] = (factor * R[i][j-1] - R[i-1][j-1]) / (factor - 1)
        
        # Check convergence
        if i > 0 and abs(R[i][i] - R[i-1][i-1]) < tol:
            return R[i][i], i
    
    return R[max_iter][max_iter], max_iter


def gaussian_quadrature(
    f: Callable[[float], float],
    a: float,
    b: float,
    n: int = 5
) -> float:
    """
    Gaussian quadrature integration.
    
    Uses n-point Gauss-Legendre quadrature.
    More accurate than Simpson for smooth functions.
    
    >>> gaussian_quadrature(lambda x: x**2, 0, 1, 3)
    0.33333333...
    """
    # Gauss-Legendre nodes and weights for standard interval [-1, 1]
    nodes_weights = {
        2: ([-0.5773502692, 0.5773502692], [1.0, 1.0]),
        3: ([-0.7745966692, 0.0, 0.7745966692], 
            [0.5555555556, 0.8888888889, 0.5555555556]),
        4: ([-0.8611363116, -0.3399810436, 0.3399810436, 0.8611363116],
            [0.3478548451, 0.6521451549, 0.6521451549, 0.3478548451]),
        5: ([-0.9061798459, -0.5384693101, 0.0, 0.5384693101, 0.9061798459],
            [0.2369268851, 0.4786286705, 0.5688888889, 0.4786286705, 0.2369268851])
    }
    
    if n not in nodes_weights:
        n = 5  # Default to 5-point
    
    nodes, weights = nodes_weights[n]
    
    # Transform from [-1, 1] to [a, b]
    result = 0
    for i in range(n):
        x = (b - a) / 2 * nodes[i] + (a + b) / 2
        result += weights[i] * f(x)
    
    return (b - a) / 2 * result


def monte_carlo(
    f: Callable[[float], float],
    a: float,
    b: float,
    n: int = 10000
) -> Tuple[float, float]:
    """
    Monte Carlo integration with error estimate.
    
    Returns (estimate, standard_error).
    
    >>> import random
    >>> random.seed(42)
    >>> result, _ = monte_carlo(lambda x: x**2, 0, 1, 100000)
    >>> abs(result - 1/3) < 0.01
    True
    """
    import random
    
    samples = [f(random.uniform(a, b)) for _ in range(n)]
    mean = sum(samples) / n
    variance = sum((s - mean) ** 2 for s in samples) / (n - 1)
    
    integral = (b - a) * mean
    std_error = (b - a) * math.sqrt(variance / n)
    
    return integral, std_error


class NumericalIntegrator:
    """
    Comprehensive numerical integration class.
    """
    
    def __init__(self, method: str = 'adaptive'):
        self.method = method
        self.methods = {
            'left': rectangular_left,
            'right': rectangular_right,
            'midpoint': midpoint,
            'trapezoid': trapezoidal,
            'simpson': simpson,
            'simpson38': simpson_38,
            'boole': boole,
            'adaptive': adaptive_simpson,
            'gauss': gaussian_quadrature
        }
    
    def integrate(
        self,
        f: Callable[[float], float],
        a: float,
        b: float,
        n: int = 100,
        tol: float = 1e-10
    ) -> float:
        """
        Integrate function f over [a, b].
        """
        if self.method == 'adaptive':
            return adaptive_simpson(f, a, b, tol)
        elif self.method == 'romberg':
            result, _ = romberg(f, a, b, tol=tol)
            return result
        else:
            return self.methods.get(self.method, simpson)(f, a, b, n)
    
    def compare_methods(
        self,
        f: Callable[[float], float],
        a: float,
        b: float,
        exact: float,
        n: int = 100
    ) -> dict:
        """
        Compare all integration methods.
        """
        results = {}
        for name, method in self.methods.items():
            try:
                if name == 'adaptive':
                    value = method(f, a, b)
                else:
                    value = method(f, a, b, n)
                error = abs(value - exact)
                results[name] = {'value': value, 'error': error}
            except Exception as e:
                results[name] = {'error': str(e)}
        
        return results
```

## 9. Applications

### 9.1 Mathematics

- Area under curves
- Arc length calculations
- Volume of revolution
- Probability density integration

### 9.2 Physics

- Work done by variable force
- Center of mass
- Moment of inertia
- Electric/magnetic flux

### 9.3 Engineering

- Signal processing
- Control systems
- Structural analysis
- Heat transfer

## 10. Real-World Software Engineering Applications

### 10.1 Industry Use Cases

1. **Finance**
   - Option pricing (Black-Scholes integral)
   - Risk metrics (VaR integration)
   - Expected value calculations

2. **Scientific Computing**
   - Simulation postprocessing
   - Numerical PDE solutions
   - Statistical analysis

3. **Machine Learning**
   - Gaussian process inference
   - Bayesian inference
   - Kernel density estimation

### 10.2 Production Example: Financial Integration

```python
from typing import Callable, Dict
import math


class FinancialIntegration:
    """
    Numerical integration for financial calculations.
    """
    
    def __init__(self, precision: float = 1e-8):
        self.precision = precision
    
    def black_scholes_d1(
        self,
        S: float,
        K: float,
        r: float,
        sigma: float,
        T: float
    ) -> float:
        """Calculate d1 for Black-Scholes."""
        return (math.log(S/K) + (r + sigma**2/2) * T) / (sigma * math.sqrt(T))
    
    def normal_cdf(self, x: float) -> float:
        """
        Standard normal CDF via numerical integration.
        
        N(x) = (1/√2π) ∫_{-∞}^{x} e^{-t²/2} dt
        
        >>> fi = FinancialIntegration()
        >>> abs(fi.normal_cdf(0) - 0.5) < 0.0001
        True
        """
        def integrand(t: float) -> float:
            return math.exp(-t**2 / 2) / math.sqrt(2 * math.pi)
        
        # Integrate from -10 (approx -∞) to x
        if x < -10:
            return 0
        if x > 10:
            return 1
        
        result = adaptive_simpson(integrand, -10, x, self.precision)
        return result
    
    def european_call_price(
        self,
        S: float,
        K: float,
        r: float,
        sigma: float,
        T: float
    ) -> float:
        """
        Black-Scholes European call option price.
        
        C = S*N(d1) - K*e^{-rT}*N(d2)
        
        >>> fi = FinancialIntegration()
        >>> price = fi.european_call_price(100, 100, 0.05, 0.2, 1)
        >>> 10 < price < 15  # Typical range
        True
        """
        d1 = self.black_scholes_d1(S, K, r, sigma, T)
        d2 = d1 - sigma * math.sqrt(T)
        
        N_d1 = self.normal_cdf(d1)
        N_d2 = self.normal_cdf(d2)
        
        return S * N_d1 - K * math.exp(-r * T) * N_d2
    
    def expected_value(
        self,
        pdf: Callable[[float], float],
        g: Callable[[float], float],
        a: float,
        b: float
    ) -> float:
        """
        Calculate E[g(X)] = ∫ g(x) * f(x) dx
        
        >>> fi = FinancialIntegration()
        >>> # E[X] for uniform(0,1)
        >>> fi.expected_value(lambda x: 1, lambda x: x, 0, 1)
        0.5
        """
        def integrand(x: float) -> float:
            return g(x) * pdf(x)
        
        return adaptive_simpson(integrand, a, b, self.precision)
    
    def var_continuous(
        self,
        pdf: Callable[[float], float],
        alpha: float,
        lower: float,
        upper: float
    ) -> float:
        """
        Value at Risk: Find x such that P(X < x) = alpha.
        
        Uses numerical integration and bisection.
        """
        def cdf(x: float) -> float:
            return adaptive_simpson(pdf, lower, x, self.precision)
        
        # Bisection to find VaR
        lo, hi = lower, upper
        while hi - lo > self.precision:
            mid = (lo + hi) / 2
            if cdf(mid) < alpha:
                lo = mid
            else:
                hi = mid
        
        return (lo + hi) / 2
    
    def duration_macaulay(
        self,
        cash_flows: Dict[float, float],
        yield_rate: float
    ) -> float:
        """
        Macaulay duration for bond.
        
        D = (1/P) × Σ t × C_t × e^{-yt}
        
        >>> fi = FinancialIntegration()
        >>> cfs = {1: 5, 2: 5, 3: 105}  # 5% coupon, 3yr bond
        >>> d = fi.duration_macaulay(cfs, 0.05)
        >>> 2.5 < d < 3  # Duration < maturity
        True
        """
        price = sum(cf * math.exp(-yield_rate * t) 
                   for t, cf in cash_flows.items())
        
        weighted = sum(t * cf * math.exp(-yield_rate * t) 
                      for t, cf in cash_flows.items())
        
        return weighted / price


# Example: Option Greeks via numerical differentiation and integration
class OptionAnalytics:
    """
    Option analytics using numerical methods.
    """
    
    def __init__(self):
        self.fi = FinancialIntegration()
    
    def price(
        self,
        S: float,
        K: float,
        r: float,
        sigma: float,
        T: float,
        option_type: str = 'call'
    ) -> float:
        """European option price."""
        call = self.fi.european_call_price(S, K, r, sigma, T)
        if option_type == 'call':
            return call
        else:
            return call - S + K * math.exp(-r * T)
    
    def delta(self, S: float, K: float, r: float, sigma: float, T: float) -> float:
        """Option delta via numerical differentiation."""
        h = 0.01
        return (self.price(S + h, K, r, sigma, T) - 
                self.price(S - h, K, r, sigma, T)) / (2 * h)
    
    def gamma(self, S: float, K: float, r: float, sigma: float, T: float) -> float:
        """Option gamma."""
        h = 0.01
        return (self.price(S + h, K, r, sigma, T) - 
                2 * self.price(S, K, r, sigma, T) +
                self.price(S - h, K, r, sigma, T)) / (h ** 2)
    
    def vega(self, S: float, K: float, r: float, sigma: float, T: float) -> float:
        """Option vega."""
        h = 0.0001
        return (self.price(S, K, r, sigma + h, T) - 
                self.price(S, K, r, sigma - h, T)) / (2 * h)


# Demo
fi = FinancialIntegration()
analytics = OptionAnalytics()

print("Option Analytics Demo")
print("=" * 40)

S, K, r, sigma, T = 100, 100, 0.05, 0.2, 1.0

print(f"Underlying: ${S}")
print(f"Strike: ${K}")
print(f"Rate: {r:.1%}")
print(f"Volatility: {sigma:.1%}")
print(f"Time: {T} year")
print()
print(f"Call Price: ${analytics.price(S, K, r, sigma, T, 'call'):.4f}")
print(f"Put Price: ${analytics.price(S, K, r, sigma, T, 'put'):.4f}")
print(f"Delta: {analytics.delta(S, K, r, sigma, T):.4f}")
print(f"Gamma: {analytics.gamma(S, K, r, sigma, T):.4f}")
print(f"Vega: {analytics.vega(S, K, r, sigma, T):.4f}")
```

## 11. Error Analysis

### 11.1 Error Bounds

| Method | Error Term |
|--------|------------|
| Trapezoid | $-\frac{(b-a)^3}{12n^2}f''(\xi)$ |
| Simpson | $-\frac{(b-a)^5}{180n^4}f^{(4)}(\xi)$ |
| Boole | $-\frac{8(b-a)^7}{945n^6}f^{(6)}(\xi)$ |

### 11.2 Richardson Extrapolation

If method has error $O(h^p)$, combining two estimates:
$$
I_{better} = \frac{2^p I(h) - I(2h)}{2^p - 1}
$$

## 12. Edge Cases

| Scenario | Solution |
|----------|----------|
| Discontinuity | Split at discontinuity |
| Singularity | Special techniques |
| Oscillatory | Adaptive or Gauss |
| Unbounded | Transform or truncate |

## 13. References

- [Wikipedia: Numerical Integration](https://en.wikipedia.org/wiki/Numerical_integration)
- Burden & Faires. "Numerical Analysis" (Ch. 4)
- Press et al. "Numerical Recipes" (Ch. 4)
