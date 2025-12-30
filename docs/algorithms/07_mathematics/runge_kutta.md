# Runge-Kutta Methods

## Overview
- **Category**: Numerical Methods for ODEs
- **Complexity**: Time: O(n × method_stages) | Space: O(1)
- **Type**: Initial value problem solver
- **Source File**: [maths/runge_kutta.py](../../../maths/runge_kutta.py)

## 1. Mathematical Foundation

### 1.1 The Problem

Solve initial value problems (IVPs):
$$
\frac{dy}{dt} = f(t, y), \quad y(t_0) = y_0
$$

Find $y(t)$ for $t > t_0$ given the derivative function $f$ and initial condition.

### 1.2 Euler's Method (First Order)

The simplest approach:
$$
y_{n+1} = y_n + h \cdot f(t_n, y_n)
$$

**Problem**: First-order accuracy, accumulates error.

### 1.3 Higher-Order Runge-Kutta

General s-stage explicit RK method:
$$
y_{n+1} = y_n + h \sum_{i=1}^{s} b_i k_i
$$

where the stages $k_i$ are:
$$
k_i = f\left(t_n + c_i h, y_n + h \sum_{j=1}^{i-1} a_{ij} k_j\right)
$$

### 1.4 Butcher Tableau

Coefficients organized as:

$$
\begin{array}{c|cccc}
c_1 & 0 & 0 & \cdots & 0 \\
c_2 & a_{21} & 0 & \cdots & 0 \\
\vdots & \vdots & \ddots & & \vdots \\
c_s & a_{s1} & a_{s2} & \cdots & 0 \\
\hline
& b_1 & b_2 & \cdots & b_s
\end{array}
$$

## 2. Common RK Methods

### 2.1 RK2 (Midpoint Method)

$$
\begin{array}{c|cc}
0 & \\
\frac{1}{2} & \frac{1}{2} \\
\hline
& 0 & 1
\end{array}
$$

$$
k_1 = f(t_n, y_n)
$$
$$
k_2 = f\left(t_n + \frac{h}{2}, y_n + \frac{h}{2}k_1\right)
$$
$$
y_{n+1} = y_n + h \cdot k_2
$$

**Error**: $O(h^2)$ per step, $O(h^2)$ global

### 2.2 RK2 (Heun's Method)

$$
\begin{array}{c|cc}
0 & \\
1 & 1 \\
\hline
& \frac{1}{2} & \frac{1}{2}
\end{array}
$$

$$
k_1 = f(t_n, y_n)
$$
$$
k_2 = f(t_n + h, y_n + h \cdot k_1)
$$
$$
y_{n+1} = y_n + \frac{h}{2}(k_1 + k_2)
$$

### 2.3 Classic RK4

The most commonly used method:

$$
\begin{array}{c|cccc}
0 & \\
\frac{1}{2} & \frac{1}{2} \\
\frac{1}{2} & 0 & \frac{1}{2} \\
1 & 0 & 0 & 1 \\
\hline
& \frac{1}{6} & \frac{1}{3} & \frac{1}{3} & \frac{1}{6}
\end{array}
$$

$$
k_1 = f(t_n, y_n)
$$
$$
k_2 = f\left(t_n + \frac{h}{2}, y_n + \frac{h}{2}k_1\right)
$$
$$
k_3 = f\left(t_n + \frac{h}{2}, y_n + \frac{h}{2}k_2\right)
$$
$$
k_4 = f(t_n + h, y_n + h \cdot k_3)
$$
$$
y_{n+1} = y_n + \frac{h}{6}(k_1 + 2k_2 + 2k_3 + k_4)
$$

**Error**: $O(h^4)$ per step, $O(h^4)$ global

### 2.4 RK45 (Runge-Kutta-Fehlberg)

Embedded method with adaptive step size:
- 4th order solution for advancing
- 5th order solution for error estimation

## 3. Pseudocode

```
ALGORITHM Euler(f, t0, y0, h, n_steps)
    INPUT: ODE f(t,y), initial (t0, y0), step h, steps n
    OUTPUT: Arrays of t and y values
    
    t[0] ← t0
    y[0] ← y0
    
    for i ← 0 to n_steps - 1 do
        y[i+1] ← y[i] + h × f(t[i], y[i])
        t[i+1] ← t[i] + h
    
    return t, y

ALGORITHM RK4(f, t0, y0, h, n_steps)
    INPUT: ODE f(t,y), initial (t0, y0), step h, steps n
    OUTPUT: Arrays of t and y values
    
    t[0] ← t0
    y[0] ← y0
    
    for i ← 0 to n_steps - 1 do
        k1 ← f(t[i], y[i])
        k2 ← f(t[i] + h/2, y[i] + h×k1/2)
        k3 ← f(t[i] + h/2, y[i] + h×k2/2)
        k4 ← f(t[i] + h, y[i] + h×k3)
        
        y[i+1] ← y[i] + h × (k1 + 2×k2 + 2×k3 + k4) / 6
        t[i+1] ← t[i] + h
    
    return t, y

ALGORITHM RK45-Adaptive(f, t0, y0, t_end, tol)
    INPUT: ODE f(t,y), initial (t0, y0), end time, tolerance
    OUTPUT: Arrays of t and y values
    
    t ← [t0]
    y ← [y0]
    h ← initial_step_estimate()
    
    while t[last] < t_end do
        y4, y5 ← rk45_step(f, t[last], y[last], h)
        error ← |y5 - y4|
        
        if error < tol then
            // Accept step
            t.append(t[last] + h)
            y.append(y5)
        
        // Adjust step size
        h_new ← h × (tol / error)^(1/5)
        h ← min(h_new, t_end - t[last])
    
    return t, y
```

## 4. Step-by-Step Example

### Example: Solve $y' = y$, $y(0) = 1$

**Exact solution**: $y(t) = e^t$

**RK4 with h = 0.1, compute y(0.1):**

$k_1 = f(0, 1) = 1$

$k_2 = f(0.05, 1 + 0.05 \cdot 1) = f(0.05, 1.05) = 1.05$

$k_3 = f(0.05, 1 + 0.05 \cdot 1.05) = f(0.05, 1.0525) = 1.0525$

$k_4 = f(0.1, 1 + 0.1 \cdot 1.0525) = f(0.1, 1.10525) = 1.10525$

$y_1 = 1 + \frac{0.1}{6}(1 + 2(1.05) + 2(1.0525) + 1.10525)$

$y_1 = 1 + \frac{0.1}{6}(6.31025) = 1.1051708...$

**Exact**: $e^{0.1} = 1.1051709...$

**Error**: $\approx 10^{-7}$ (excellent!)

## 5. Complexity Analysis

| Method | Order | f-evals/step | Memory | Global Error |
|--------|-------|--------------|--------|--------------|
| Euler | 1 | 1 | O(1) | O(h) |
| RK2 | 2 | 2 | O(1) | O(h²) |
| RK4 | 4 | 4 | O(1) | O(h⁴) |
| RK45 | 4-5 | 6 | O(1) | O(h⁴) |

### 5.1 Efficiency

Work to achieve global error $\epsilon$:
- Euler: $O(1/\epsilon)$ evaluations
- RK4: $O(1/\epsilon^{1/4})$ evaluations

RK4 is much more efficient for small errors!

## 6. Visual Representation

### 6.1 RK4 Stage Points

```
      y
      │
 y+k₄ ●─────────────●  y_{n+1} (weighted average)
      │           /
 y+k₃ │   ●─────/
      │   │   /
 y+k₂ │   ●─/
      │   │/
 y+k₁ │   ●
      │  /│
   yₙ ●───┼───────────────
      │   │               │
      └───┼───────────────┼──── t
         tₙ  tₙ+h/2      tₙ+h
```

### 6.2 Error Comparison

```
Error
  │
  │ *                      Euler
  │  *
  │   *    *              RK2
  │     *    *
  │       *    *   *      RK4
  │          *   *  *  *
  └─────────────────────── h
        Small ← Step size → Large
```

## 7. Implementation

```python
from typing import Callable, List, Tuple, Optional
import math
from dataclasses import dataclass


# Type alias for ODE function
ODEFunc = Callable[[float, float], float]
ODEFuncVector = Callable[[float, List[float]], List[float]]


def euler(
    f: ODEFunc,
    t0: float,
    y0: float,
    h: float,
    n_steps: int
) -> Tuple[List[float], List[float]]:
    """
    Euler's method for solving y' = f(t, y).
    
    First-order method. Simple but inaccurate.
    
    >>> def f(t, y): return y  # y' = y
    >>> t, y = euler(f, 0, 1, 0.1, 10)
    >>> abs(y[-1] - math.e) < 0.2  # ~18% error
    True
    """
    t = [t0]
    y = [y0]
    
    for _ in range(n_steps):
        y_new = y[-1] + h * f(t[-1], y[-1])
        t.append(t[-1] + h)
        y.append(y_new)
    
    return t, y


def rk2_midpoint(
    f: ODEFunc,
    t0: float,
    y0: float,
    h: float,
    n_steps: int
) -> Tuple[List[float], List[float]]:
    """
    RK2 Midpoint method.
    
    Second-order Runge-Kutta.
    
    >>> def f(t, y): return y
    >>> t, y = rk2_midpoint(f, 0, 1, 0.1, 10)
    >>> abs(y[-1] - math.e) < 0.02  # ~0.5% error
    True
    """
    t = [t0]
    y = [y0]
    
    for _ in range(n_steps):
        k1 = f(t[-1], y[-1])
        k2 = f(t[-1] + h/2, y[-1] + h*k1/2)
        
        y_new = y[-1] + h * k2
        t.append(t[-1] + h)
        y.append(y_new)
    
    return t, y


def rk2_heun(
    f: ODEFunc,
    t0: float,
    y0: float,
    h: float,
    n_steps: int
) -> Tuple[List[float], List[float]]:
    """
    RK2 Heun's method (improved Euler).
    
    >>> def f(t, y): return y
    >>> t, y = rk2_heun(f, 0, 1, 0.1, 10)
    >>> abs(y[-1] - math.e) < 0.02
    True
    """
    t = [t0]
    y = [y0]
    
    for _ in range(n_steps):
        k1 = f(t[-1], y[-1])
        k2 = f(t[-1] + h, y[-1] + h*k1)
        
        y_new = y[-1] + h * (k1 + k2) / 2
        t.append(t[-1] + h)
        y.append(y_new)
    
    return t, y


def rk4(
    f: ODEFunc,
    t0: float,
    y0: float,
    h: float,
    n_steps: int
) -> Tuple[List[float], List[float]]:
    """
    Classic 4th-order Runge-Kutta method.
    
    The most commonly used ODE solver.
    
    >>> def f(t, y): return y  # y' = y, solution: y = e^t
    >>> t, y = rk4(f, 0, 1, 0.1, 10)
    >>> abs(y[-1] - math.e) < 1e-5  # Very accurate!
    True
    
    >>> def f(t, y): return -2*y  # y' = -2y, solution: y = e^{-2t}
    >>> t, y = rk4(f, 0, 1, 0.1, 10)
    >>> abs(y[-1] - math.exp(-2)) < 1e-5
    True
    """
    t = [t0]
    y = [y0]
    
    for _ in range(n_steps):
        k1 = f(t[-1], y[-1])
        k2 = f(t[-1] + h/2, y[-1] + h*k1/2)
        k3 = f(t[-1] + h/2, y[-1] + h*k2/2)
        k4 = f(t[-1] + h, y[-1] + h*k3)
        
        y_new = y[-1] + h * (k1 + 2*k2 + 2*k3 + k4) / 6
        t.append(t[-1] + h)
        y.append(y_new)
    
    return t, y


def rk4_vector(
    f: ODEFuncVector,
    t0: float,
    y0: List[float],
    h: float,
    n_steps: int
) -> Tuple[List[float], List[List[float]]]:
    """
    RK4 for systems of ODEs.
    
    Solves y' = f(t, y) where y is a vector.
    
    >>> # Simple harmonic oscillator: y'' + y = 0
    >>> # Convert to system: y1' = y2, y2' = -y1
    >>> def f(t, y): return [y[1], -y[0]]
    >>> t, y = rk4_vector(f, 0, [0, 1], 0.01, 628)  # ~2π
    >>> abs(y[-1][0] - 0) < 0.01  # Back to 0
    True
    """
    n = len(y0)
    t = [t0]
    y = [y0.copy()]
    
    for _ in range(n_steps):
        y_curr = y[-1]
        t_curr = t[-1]
        
        k1 = f(t_curr, y_curr)
        
        y_temp = [y_curr[i] + h*k1[i]/2 for i in range(n)]
        k2 = f(t_curr + h/2, y_temp)
        
        y_temp = [y_curr[i] + h*k2[i]/2 for i in range(n)]
        k3 = f(t_curr + h/2, y_temp)
        
        y_temp = [y_curr[i] + h*k3[i] for i in range(n)]
        k4 = f(t_curr + h, y_temp)
        
        y_new = [
            y_curr[i] + h * (k1[i] + 2*k2[i] + 2*k3[i] + k4[i]) / 6
            for i in range(n)
        ]
        
        t.append(t_curr + h)
        y.append(y_new)
    
    return t, y


def rk45_adaptive(
    f: ODEFunc,
    t0: float,
    y0: float,
    t_end: float,
    tol: float = 1e-6,
    h_init: float = 0.1,
    h_min: float = 1e-10,
    h_max: float = 1.0
) -> Tuple[List[float], List[float]]:
    """
    Runge-Kutta-Fehlberg 4(5) with adaptive step size.
    
    Automatically adjusts step size to maintain error tolerance.
    
    >>> def f(t, y): return y
    >>> t, y = rk45_adaptive(f, 0, 1, 1, tol=1e-8)
    >>> abs(y[-1] - math.e) < 1e-7
    True
    """
    # RK45 Coefficients (Fehlberg)
    a2, a3, a4, a5, a6 = 1/4, 3/8, 12/13, 1, 1/2
    
    b21 = 1/4
    b31, b32 = 3/32, 9/32
    b41, b42, b43 = 1932/2197, -7200/2197, 7296/2197
    b51, b52, b53, b54 = 439/216, -8, 3680/513, -845/4104
    b61, b62, b63, b64, b65 = -8/27, 2, -3544/2565, 1859/4104, -11/40
    
    # 4th order weights
    c1, c3, c4, c5 = 25/216, 1408/2565, 2197/4104, -1/5
    
    # 5th order weights
    d1, d3, d4, d5, d6 = 16/135, 6656/12825, 28561/56430, -9/50, 2/55
    
    t = [t0]
    y = [y0]
    h = h_init
    
    while t[-1] < t_end:
        t_curr = t[-1]
        y_curr = y[-1]
        
        # Don't overshoot
        if t_curr + h > t_end:
            h = t_end - t_curr
        
        # Compute stages
        k1 = h * f(t_curr, y_curr)
        k2 = h * f(t_curr + a2*h, y_curr + b21*k1)
        k3 = h * f(t_curr + a3*h, y_curr + b31*k1 + b32*k2)
        k4 = h * f(t_curr + a4*h, y_curr + b41*k1 + b42*k2 + b43*k3)
        k5 = h * f(t_curr + a5*h, y_curr + b51*k1 + b52*k2 + b53*k3 + b54*k4)
        k6 = h * f(t_curr + a6*h, y_curr + b61*k1 + b62*k2 + b63*k3 + b64*k4 + b65*k5)
        
        # 4th order solution
        y4 = y_curr + c1*k1 + c3*k3 + c4*k4 + c5*k5
        
        # 5th order solution
        y5 = y_curr + d1*k1 + d3*k3 + d4*k4 + d5*k5 + d6*k6
        
        # Error estimate
        error = abs(y5 - y4)
        
        if error < tol or h <= h_min:
            # Accept step
            t.append(t_curr + h)
            y.append(y5)
        
        # Adjust step size
        if error > 0:
            h_new = 0.9 * h * (tol / error) ** 0.2
            h = max(h_min, min(h_max, h_new))
    
    return t, y


@dataclass
class ODESolution:
    """Solution container for ODE solver."""
    t: List[float]
    y: List[float]
    method: str
    n_evals: int
    
    def at(self, t_query: float) -> float:
        """Interpolate solution at given time."""
        # Simple linear interpolation
        for i in range(len(self.t) - 1):
            if self.t[i] <= t_query <= self.t[i + 1]:
                alpha = (t_query - self.t[i]) / (self.t[i + 1] - self.t[i])
                return self.y[i] * (1 - alpha) + self.y[i + 1] * alpha
        return self.y[-1]


class ODESolver:
    """
    Comprehensive ODE solver class.
    """
    
    METHODS = {
        'euler': (euler, 1),
        'rk2_midpoint': (rk2_midpoint, 2),
        'rk2_heun': (rk2_heun, 2),
        'rk4': (rk4, 4),
    }
    
    def solve(
        self,
        f: ODEFunc,
        t_span: Tuple[float, float],
        y0: float,
        method: str = 'rk4',
        n_steps: int = 100,
        adaptive: bool = False,
        tol: float = 1e-6
    ) -> ODESolution:
        """
        Solve ODE y' = f(t, y) with y(t0) = y0.
        
        >>> solver = ODESolver()
        >>> sol = solver.solve(lambda t, y: y, (0, 1), 1)
        >>> abs(sol.y[-1] - math.e) < 1e-5
        True
        """
        t0, t_end = t_span
        
        if adaptive:
            t, y = rk45_adaptive(f, t0, y0, t_end, tol)
            return ODESolution(t, y, 'rk45_adaptive', len(t) * 6)
        
        h = (t_end - t0) / n_steps
        method_func, order = self.METHODS.get(method, (rk4, 4))
        
        t, y = method_func(f, t0, y0, h, n_steps)
        
        return ODESolution(t, y, method, n_steps * order)
    
    def solve_system(
        self,
        f: ODEFuncVector,
        t_span: Tuple[float, float],
        y0: List[float],
        n_steps: int = 100
    ) -> Tuple[List[float], List[List[float]]]:
        """
        Solve system of ODEs using RK4.
        """
        t0, t_end = t_span
        h = (t_end - t0) / n_steps
        return rk4_vector(f, t0, y0, h, n_steps)
    
    def compare_methods(
        self,
        f: ODEFunc,
        exact: Callable[[float], float],
        t_span: Tuple[float, float],
        y0: float,
        n_steps: int = 100
    ) -> dict:
        """
        Compare accuracy of different methods.
        """
        results = {}
        
        for name in self.METHODS:
            sol = self.solve(f, t_span, y0, method=name, n_steps=n_steps)
            exact_final = exact(sol.t[-1])
            error = abs(sol.y[-1] - exact_final)
            results[name] = {
                'value': sol.y[-1],
                'exact': exact_final,
                'error': error,
                'n_evals': sol.n_evals
            }
        
        return results
```

## 8. Applications

### 8.1 Physics

- Projectile motion with drag
- Planetary orbits (N-body)
- Oscillations (spring, pendulum)
- Heat equation (method of lines)

### 8.2 Engineering

- Circuit analysis (RC, RLC)
- Control systems
- Structural dynamics
- Chemical kinetics

### 8.3 Biology

- Population dynamics
- Epidemiology (SIR models)
- Pharmacokinetics
- Neural models

## 9. Real-World Software Engineering Applications

### 9.1 Industry Use Cases

1. **Game Physics**
   - Character movement
   - Projectile simulation
   - Cloth/fluid dynamics

2. **Financial Modeling**
   - Option pricing (Black-Scholes PDE)
   - Interest rate models
   - Risk simulation

3. **Control Systems**
   - PID controller simulation
   - Robot arm dynamics
   - Autonomous vehicles

### 9.2 Production Example: Physics Simulation Engine

```python
from typing import List, Tuple, Callable
from dataclasses import dataclass
import math


@dataclass
class PhysicsState:
    """State of a physics object."""
    position: List[float]  # [x, y, z]
    velocity: List[float]  # [vx, vy, vz]
    
    def to_vector(self) -> List[float]:
        return self.position + self.velocity
    
    @classmethod
    def from_vector(cls, v: List[float]) -> 'PhysicsState':
        return cls(v[:3], v[3:])


class PhysicsSimulator:
    """
    Physics simulation using RK4 integration.
    """
    
    def __init__(self, dt: float = 0.01):
        self.dt = dt
        self.gravity = [0, -9.81, 0]
        self.drag_coefficient = 0.0
    
    def _derivatives(
        self, 
        t: float, 
        state: List[float],
        forces: Callable[[float, List[float]], List[float]]
    ) -> List[float]:
        """
        Compute derivatives for RK4.
        
        state = [x, y, z, vx, vy, vz]
        derivatives = [vx, vy, vz, ax, ay, az]
        """
        pos = state[:3]
        vel = state[3:]
        
        # Get forces (returns acceleration)
        acc = forces(t, state)
        
        return vel + acc
    
    def rk4_step(
        self,
        t: float,
        state: List[float],
        forces: Callable[[float, List[float]], List[float]]
    ) -> List[float]:
        """Single RK4 step."""
        h = self.dt
        
        k1 = self._derivatives(t, state, forces)
        
        state2 = [state[i] + h*k1[i]/2 for i in range(6)]
        k2 = self._derivatives(t + h/2, state2, forces)
        
        state3 = [state[i] + h*k2[i]/2 for i in range(6)]
        k3 = self._derivatives(t + h/2, state3, forces)
        
        state4 = [state[i] + h*k3[i] for i in range(6)]
        k4 = self._derivatives(t + h, state4, forces)
        
        return [
            state[i] + h * (k1[i] + 2*k2[i] + 2*k3[i] + k4[i]) / 6
            for i in range(6)
        ]
    
    def simulate_projectile(
        self,
        initial_pos: List[float],
        initial_vel: List[float],
        t_max: float,
        mass: float = 1.0,
        drag: float = 0.0
    ) -> Tuple[List[float], List[PhysicsState]]:
        """
        Simulate projectile motion with gravity and optional drag.
        
        >>> sim = PhysicsSimulator(dt=0.01)
        >>> t, states = sim.simulate_projectile([0, 0, 0], [10, 10, 0], 2.0)
        >>> states[-1].position[1] < 0  # Falls below ground
        True
        """
        def forces(t: float, state: List[float]) -> List[float]:
            vel = state[3:]
            speed = math.sqrt(sum(v**2 for v in vel))
            
            # Gravity
            acc = list(self.gravity)
            
            # Drag: F = -c * v^2 * v_hat
            if drag > 0 and speed > 0:
                for i in range(3):
                    acc[i] -= drag * speed * vel[i] / mass
            
            return acc
        
        state = initial_pos + initial_vel
        states = [PhysicsState.from_vector(state)]
        times = [0.0]
        
        t = 0.0
        while t < t_max:
            state = self.rk4_step(t, state, forces)
            t += self.dt
            
            times.append(t)
            states.append(PhysicsState.from_vector(state))
            
            # Stop if hit ground
            if state[1] < 0:
                break
        
        return times, states
    
    def simulate_pendulum(
        self,
        length: float,
        theta0: float,
        omega0: float,
        t_max: float,
        damping: float = 0.0
    ) -> Tuple[List[float], List[float], List[float]]:
        """
        Simulate simple pendulum.
        
        θ'' = -(g/L)sin(θ) - c*θ'
        
        >>> sim = PhysicsSimulator(dt=0.001)
        >>> t, theta, omega = sim.simulate_pendulum(1.0, 0.1, 0, 10.0)
        >>> max(abs(th) for th in theta) < 0.15  # Small angle, amplitude preserved
        True
        """
        g = 9.81
        
        def f(t: float, y: List[float]) -> List[float]:
            theta, omega = y
            dtheta = omega
            domega = -(g / length) * math.sin(theta) - damping * omega
            return [dtheta, domega]
        
        state = [theta0, omega0]
        times = [0.0]
        thetas = [theta0]
        omegas = [omega0]
        
        t = 0.0
        while t < t_max:
            h = self.dt
            
            k1 = f(t, state)
            k2 = f(t + h/2, [state[i] + h*k1[i]/2 for i in range(2)])
            k3 = f(t + h/2, [state[i] + h*k2[i]/2 for i in range(2)])
            k4 = f(t + h, [state[i] + h*k3[i] for i in range(2)])
            
            state = [
                state[i] + h * (k1[i] + 2*k2[i] + 2*k3[i] + k4[i]) / 6
                for i in range(2)
            ]
            
            t += self.dt
            times.append(t)
            thetas.append(state[0])
            omegas.append(state[1])
        
        return times, thetas, omegas
    
    def simulate_orbit(
        self,
        pos0: List[float],
        vel0: List[float],
        central_mass: float,
        t_max: float,
        G: float = 6.674e-11
    ) -> Tuple[List[float], List[List[float]]]:
        """
        Simulate orbital mechanics around central mass.
        
        >>> sim = PhysicsSimulator(dt=100)
        >>> # Earth-like orbit (simplified units)
        >>> t, pos = sim.simulate_orbit([1e11, 0, 0], [0, 3e4, 0], 2e30, 1e7, G=6.674e-11)
        >>> len(pos) > 0
        True
        """
        def forces(t: float, state: List[float]) -> List[float]:
            pos = state[:3]
            r = math.sqrt(sum(p**2 for p in pos))
            
            if r < 1e-10:  # Avoid singularity
                return [0, 0, 0]
            
            # Gravitational acceleration
            a_mag = -G * central_mass / (r ** 2)
            return [a_mag * p / r for p in pos]
        
        state = pos0 + vel0
        positions = [pos0.copy()]
        times = [0.0]
        
        t = 0.0
        while t < t_max:
            state = self.rk4_step(t, state, forces)
            t += self.dt
            
            times.append(t)
            positions.append(state[:3])
        
        return times, positions


class EpidemiologyModel:
    """
    SIR epidemiology model using RK4.
    """
    
    def __init__(self, beta: float, gamma: float):
        """
        Args:
            beta: Infection rate
            gamma: Recovery rate
        """
        self.beta = beta
        self.gamma = gamma
    
    def sir_derivatives(self, t: float, y: List[float]) -> List[float]:
        """
        SIR model equations:
        dS/dt = -beta * S * I
        dI/dt = beta * S * I - gamma * I
        dR/dt = gamma * I
        """
        S, I, R = y
        N = S + I + R
        
        dS = -self.beta * S * I / N
        dI = self.beta * S * I / N - self.gamma * I
        dR = self.gamma * I
        
        return [dS, dI, dR]
    
    def simulate(
        self,
        S0: float,
        I0: float,
        R0: float,
        t_max: float,
        dt: float = 0.1
    ) -> Tuple[List[float], List[List[float]]]:
        """
        Run SIR simulation.
        
        >>> model = EpidemiologyModel(beta=0.3, gamma=0.1)
        >>> t, y = model.simulate(999, 1, 0, 100)
        >>> y[-1][1] < 1  # Infection dies out
        True
        """
        state = [S0, I0, R0]
        history = [[S0, I0, R0]]
        times = [0.0]
        
        t = 0.0
        while t < t_max:
            h = dt
            
            k1 = self.sir_derivatives(t, state)
            k2 = self.sir_derivatives(t + h/2, [state[i] + h*k1[i]/2 for i in range(3)])
            k3 = self.sir_derivatives(t + h/2, [state[i] + h*k2[i]/2 for i in range(3)])
            k4 = self.sir_derivatives(t + h, [state[i] + h*k3[i] for i in range(3)])
            
            state = [
                state[i] + h * (k1[i] + 2*k2[i] + 2*k3[i] + k4[i]) / 6
                for i in range(3)
            ]
            
            t += dt
            times.append(t)
            history.append(state.copy())
        
        return times, history
    
    def R0_effective(self) -> float:
        """Basic reproduction number."""
        return self.beta / self.gamma


# Demo
print("Physics Simulation Demo")
print("=" * 40)

sim = PhysicsSimulator(dt=0.01)

# Projectile
t, states = sim.simulate_projectile([0, 0, 0], [20, 30, 0], 10.0)
max_height = max(s.position[1] for s in states)
landing_time = t[-1]
landing_dist = states[-1].position[0]

print(f"Projectile simulation:")
print(f"  Max height: {max_height:.2f} m")
print(f"  Flight time: {landing_time:.2f} s")
print(f"  Landing distance: {landing_dist:.2f} m")

print()

# Pendulum
t, theta, omega = sim.simulate_pendulum(1.0, 0.5, 0, 10.0)
period = None
for i in range(1, len(theta)):
    if theta[i-1] < 0 and theta[i] >= 0:
        period = 2 * t[i]
        break

print(f"Pendulum (L=1m, θ₀=0.5 rad):")
print(f"  Approximate period: {period:.3f} s")
print(f"  Theoretical (small angle): {2*math.pi*math.sqrt(1/9.81):.3f} s")

print()

# SIR model
model = EpidemiologyModel(beta=0.3, gamma=0.1)
t, history = model.simulate(999, 1, 0, 100)
peak_infected = max(h[1] for h in history)
peak_time = t[history.index([h for h in history if h[1] == peak_infected][0])]

print(f"SIR Epidemic Model (β=0.3, γ=0.1, R₀={model.R0_effective()}):")
print(f"  Peak infected: {peak_infected:.0f}")
print(f"  Peak time: {peak_time:.1f} days")
print(f"  Final recovered: {history[-1][2]:.0f}")
```

## 10. Stability Analysis

### 10.1 Stability Region

A method is stable for $y' = \lambda y$ if $|y_{n+1}/y_n| < 1$.

For RK4 applied to $y' = \lambda y$:
$$
y_{n+1} = R(h\lambda) y_n
$$

where $R(z) = 1 + z + \frac{z^2}{2} + \frac{z^3}{6} + \frac{z^4}{24}$

### 10.2 Stiff Equations

For stiff problems (widely varying timescales), implicit methods are preferred:
- Backward Euler
- Implicit RK (DIRK, SDIRK)
- BDF methods

## 11. Edge Cases

| Scenario | Solution |
|----------|----------|
| Stiff equations | Use implicit methods |
| Discontinuities | Restart at discontinuity |
| Singularities | Use regularization |
| Conservation laws | Use symplectic integrators |

## 12. References

- [Wikipedia: Runge-Kutta Methods](https://en.wikipedia.org/wiki/Runge%E2%80%93Kutta_methods)
- Hairer, Nørsett, Wanner. "Solving Ordinary Differential Equations I"
- Butcher. "Numerical Methods for Ordinary Differential Equations"
