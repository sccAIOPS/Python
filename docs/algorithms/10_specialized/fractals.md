# Fractal Algorithms

## Overview
- **Category**: Computational Geometry / Graphics
- **Complexity**: Time: O(n) to O(n²) per iteration | Space: O(n)
- **Type**: Recursive geometry, self-similarity
- **Source Files**: [fractals/](../../../fractals/)

## 1. Mathematical Foundation

### 1.1 Self-Similarity

A fractal exhibits self-similarity at different scales:
$$
F = \bigcup_{i=1}^{N} S_i(F)
$$

Where $S_i$ are contraction mappings.

### 1.2 Fractal Dimension

**Hausdorff Dimension:**
$$
D = \frac{\log N}{\log (1/r)}
$$

Where $N$ = number of self-similar pieces, $r$ = scaling ratio.

| Fractal | Dimension |
|---------|-----------|
| Cantor Set | 0.631 |
| Sierpiński Triangle | 1.585 |
| Koch Curve | 1.262 |
| Mandelbrot Set | 2.0 |

### 1.3 Mandelbrot Set

Complex numbers $c$ where iteration stays bounded:
$$
z_{n+1} = z_n^2 + c, \quad z_0 = 0
$$

Point $c$ is in the set if $|z_n| \leq 2$ for all $n$.

### 1.4 Julia Sets

For fixed $c$, iterate:
$$
z_{n+1} = z_n^2 + c
$$

Starting from various $z_0$ values. Connected if $c$ is in Mandelbrot set.

### 1.5 L-Systems

Grammar-based fractal description:
- **Axiom**: Initial string
- **Rules**: Production rules for substitution
- **Interpretation**: Turtle graphics commands

Example (Koch curve):
- Axiom: F
- Rule: F → F+F−F−F+F

### 1.6 Iterated Function Systems (IFS)

Set of affine transformations:
$$
\begin{pmatrix} x' \\ y' \end{pmatrix} = 
\begin{pmatrix} a & b \\ c & d \end{pmatrix}
\begin{pmatrix} x \\ y \end{pmatrix} +
\begin{pmatrix} e \\ f \end{pmatrix}
$$

Each transformation applied with probability $p_i$.

## 2. Fractal Categories

### 2.1 Escape-Time Fractals
- Mandelbrot Set
- Julia Sets
- Burning Ship
- Tricorn

### 2.2 Iterated Function Systems
- Sierpiński Triangle
- Barnsley Fern
- Dragon Curve

### 2.3 L-System Fractals
- Koch Snowflake
- Hilbert Curve
- Plant-like structures

### 2.4 Strange Attractors
- Lorenz Attractor
- Hénon Map
- Rössler Attractor

## 3. Pseudocode

### 3.1 Mandelbrot Set

```
ALGORITHM Mandelbrot(width, height, x_min, x_max, y_min, y_max, max_iter)
    INPUT: Image dimensions, complex plane bounds, max iterations
    OUTPUT: 2D array of escape times
    
    image ← 2D array [height][width]
    
    for py ← 0 to height - 1 do
        for px ← 0 to width - 1 do
            // Map pixel to complex plane
            x0 ← x_min + (x_max - x_min) * px / width
            y0 ← y_min + (y_max - y_min) * py / height
            
            x ← 0, y ← 0
            iteration ← 0
            
            while x² + y² ≤ 4 AND iteration < max_iter do
                x_temp ← x² - y² + x0
                y ← 2*x*y + y0
                x ← x_temp
                iteration ← iteration + 1
            
            image[py][px] ← iteration
    
    return image
```

### 3.2 Sierpiński Triangle (Chaos Game)

```
ALGORITHM SierpinskiChaosGame(iterations, vertices)
    INPUT: Number of iterations, triangle vertices
    OUTPUT: Set of points
    
    points ← []
    current ← random point inside triangle
    
    for i ← 1 to iterations do
        vertex ← random choice from vertices
        current ← midpoint(current, vertex)
        points.add(current)
    
    return points
```

### 3.3 Koch Curve

```
ALGORITHM KochCurve(start, end, depth)
    INPUT: Start point, end point, recursion depth
    OUTPUT: List of points forming curve
    
    if depth = 0 then
        return [start, end]
    
    // Divide line into thirds
    p1 ← start + (end - start) / 3
    p2 ← start + 2 * (end - start) / 3
    
    // Create peak point (equilateral triangle)
    angle ← -60°
    peak ← rotate(p2 - p1, angle) + p1
    
    // Recurse on 4 segments
    curve ← []
    curve.extend(KochCurve(start, p1, depth - 1))
    curve.extend(KochCurve(p1, peak, depth - 1))
    curve.extend(KochCurve(peak, p2, depth - 1))
    curve.extend(KochCurve(p2, end, depth - 1))
    
    return curve
```

### 3.4 L-System

```
ALGORITHM LSystem(axiom, rules, iterations)
    INPUT: Starting string, production rules, iterations
    OUTPUT: Final string
    
    current ← axiom
    
    for i ← 1 to iterations do
        next ← ""
        for each char in current do
            if char in rules then
                next ← next + rules[char]
            else
                next ← next + char
        current ← next
    
    return current


ALGORITHM InterpretLSystem(instructions, angle, length)
    INPUT: L-system string, turn angle, step length
    OUTPUT: List of line segments
    
    position ← (0, 0)
    direction ← 0
    stack ← []
    lines ← []
    
    for each char in instructions do
        if char = 'F' then
            // Draw forward
            new_pos ← position + length * (cos(direction), sin(direction))
            lines.add((position, new_pos))
            position ← new_pos
        else if char = '+' then
            direction ← direction + angle
        else if char = '-' then
            direction ← direction - angle
        else if char = '[' then
            stack.push((position, direction))
        else if char = ']' then
            (position, direction) ← stack.pop()
    
    return lines
```

### 3.5 Barnsley Fern (IFS)

```
ALGORITHM BarnsleyFern(iterations)
    INPUT: Number of points to generate
    OUTPUT: List of (x, y) points
    
    points ← []
    x, y ← 0, 0
    
    for i ← 1 to iterations do
        r ← random(0, 100)
        
        if r < 1 then
            // Stem
            x_new ← 0
            y_new ← 0.16 * y
        else if r < 86 then
            // Successively smaller leaflets
            x_new ← 0.85*x + 0.04*y
            y_new ← -0.04*x + 0.85*y + 1.6
        else if r < 93 then
            // Largest left-hand leaflet
            x_new ← 0.2*x - 0.26*y
            y_new ← 0.23*x + 0.22*y + 1.6
        else
            // Largest right-hand leaflet
            x_new ← -0.15*x + 0.28*y
            y_new ← 0.26*x + 0.24*y + 0.44
        
        x, y ← x_new, y_new
        points.add((x, y))
    
    return points
```

## 4. Step-by-Step Example

### Example 1: Mandelbrot Escape Time

```
Point c = -0.5 + 0.5i

Iteration 0: z = 0 + 0i
  |z| = 0 ≤ 2 ✓

Iteration 1: z = z² + c = 0 + (-0.5 + 0.5i) = -0.5 + 0.5i
  |z| = √(0.25 + 0.25) = 0.707 ≤ 2 ✓

Iteration 2: z = (-0.5 + 0.5i)² + (-0.5 + 0.5i)
           = (0.25 - 0.25 - 0.5i) + (-0.5 + 0.5i)
           = -0.5 + 0i
  |z| = 0.5 ≤ 2 ✓

Iteration 3: z = (-0.5)² + (-0.5 + 0.5i) = -0.25 + 0.5i
  |z| = 0.559 ≤ 2 ✓

... continues, never escapes → c is IN Mandelbrot set
```

### Example 2: Koch Curve Construction

```
Iteration 0: _______

Iteration 1: __/\__

Iteration 2:   /\
            _/  \_
             /\
            /  \

Each segment is replaced by 4 segments at 1/3 scale.
```

### Example 3: Sierpiński Chaos Game

```
Vertices: A(0, 0), B(1, 0), C(0.5, 0.866)

Start: P(0.3, 0.2)

Step 1: Choose B randomly
  New P = midpoint(P, B) = (0.65, 0.1)

Step 2: Choose C randomly
  New P = midpoint(P, C) = (0.575, 0.483)

Step 3: Choose A randomly
  New P = midpoint(P, A) = (0.288, 0.242)

... after many iterations, forms Sierpiński triangle
```

## 5. Complexity Analysis

| Algorithm | Time | Space | Quality Factor |
|-----------|------|-------|----------------|
| Mandelbrot (naive) | O(w×h×max_iter) | O(w×h) | max_iter |
| Mandelbrot (perturbation) | O(w×h) | O(w×h) | Zoom level |
| Sierpiński (chaos) | O(n) | O(n) | n = points |
| Koch curve | O(4^d) | O(4^d) | d = depth |
| Barnsley Fern | O(n) | O(n) | n = points |
| L-System | O(k^n) | O(k^n) | k = expansion |

## 6. Visual Representation

### 6.1 Mandelbrot Set Region

```
                    ████████████                    
                 █████████████████                  
               ████████████████████                 
             ██████████████████████████             
           ███████████████████████████████          
          █████████████████████████████████         
        ███████████████████████████████████████     
       █████████████████████████████████████████    
    ██████████████████████████████████████████████  
████████████████████████████████████████████████████
    ██████████████████████████████████████████████  
       █████████████████████████████████████████    
        ███████████████████████████████████████     
          █████████████████████████████████         
           ███████████████████████████████          
             ██████████████████████████             
               ████████████████████                 
                 █████████████████                  
                    ████████████                    
```

### 6.2 Sierpiński Triangle

```
        △
       △ △
      △   △
     △ △ △ △
    △       △
   △ △     △ △
  △   △   △   △
 △ △ △ △ △ △ △ △
```

### 6.3 Koch Snowflake

```
       *
      /\
     /  \
    *    *
   /\    /\
  /  \  /  \
 *----**----*
```

## 7. Implementation

```python
from typing import List, Tuple, Dict, Optional, Callable
from dataclasses import dataclass
import math
import cmath


def mandelbrot(
    width: int,
    height: int,
    x_min: float = -2.5,
    x_max: float = 1.0,
    y_min: float = -1.25,
    y_max: float = 1.25,
    max_iter: int = 100
) -> List[List[int]]:
    """
    Generate Mandelbrot set escape time image.
    
    >>> img = mandelbrot(10, 10, max_iter=20)
    >>> len(img), len(img[0])
    (10, 10)
    """
    image = [[0] * width for _ in range(height)]
    
    for py in range(height):
        for px in range(width):
            # Map pixel to complex plane
            x0 = x_min + (x_max - x_min) * px / width
            y0 = y_min + (y_max - y_min) * py / height
            
            x, y = 0.0, 0.0
            iteration = 0
            
            while x*x + y*y <= 4 and iteration < max_iter:
                x_temp = x*x - y*y + x0
                y = 2*x*y + y0
                x = x_temp
                iteration += 1
            
            image[py][px] = iteration
    
    return image


def julia_set(
    width: int,
    height: int,
    c: complex,
    x_min: float = -2.0,
    x_max: float = 2.0,
    y_min: float = -2.0,
    y_max: float = 2.0,
    max_iter: int = 100
) -> List[List[int]]:
    """
    Generate Julia set for given c value.
    
    >>> img = julia_set(10, 10, complex(-0.7, 0.27))
    >>> all(0 <= img[i][j] <= 100 for i in range(10) for j in range(10))
    True
    """
    image = [[0] * width for _ in range(height)]
    
    for py in range(height):
        for px in range(width):
            # Map pixel to complex plane
            zx = x_min + (x_max - x_min) * px / width
            zy = y_min + (y_max - y_min) * py / height
            
            z = complex(zx, zy)
            iteration = 0
            
            while abs(z) <= 2 and iteration < max_iter:
                z = z * z + c
                iteration += 1
            
            image[py][px] = iteration
    
    return image


def sierpinski_chaos_game(
    iterations: int = 10000,
    vertices: List[Tuple[float, float]] = None
) -> List[Tuple[float, float]]:
    """
    Generate Sierpiński triangle using chaos game.
    
    >>> points = sierpinski_chaos_game(100)
    >>> len(points)
    100
    """
    import random
    
    if vertices is None:
        vertices = [(0, 0), (1, 0), (0.5, math.sqrt(3)/2)]
    
    points = []
    x, y = random.random(), random.random()
    
    for _ in range(iterations):
        # Choose random vertex
        vx, vy = random.choice(vertices)
        
        # Move halfway to vertex
        x = (x + vx) / 2
        y = (y + vy) / 2
        
        points.append((x, y))
    
    return points


def koch_curve(
    start: Tuple[float, float],
    end: Tuple[float, float],
    depth: int
) -> List[Tuple[float, float]]:
    """
    Generate Koch curve points.
    
    >>> points = koch_curve((0, 0), (1, 0), 2)
    >>> len(points) > 2
    True
    """
    if depth == 0:
        return [start, end]
    
    x1, y1 = start
    x2, y2 = end
    
    # Calculate division points
    dx = (x2 - x1) / 3
    dy = (y2 - y1) / 3
    
    p1 = (x1 + dx, y1 + dy)
    p2 = (x1 + 2*dx, y1 + 2*dy)
    
    # Calculate peak (60 degree rotation)
    angle = math.pi / 3
    px = p1[0] + dx * math.cos(angle) - dy * math.sin(angle)
    py = p1[1] + dx * math.sin(angle) + dy * math.cos(angle)
    peak = (px, py)
    
    # Recurse
    points = []
    points.extend(koch_curve(start, p1, depth - 1)[:-1])
    points.extend(koch_curve(p1, peak, depth - 1)[:-1])
    points.extend(koch_curve(peak, p2, depth - 1)[:-1])
    points.extend(koch_curve(p2, end, depth - 1))
    
    return points


def koch_snowflake(
    center: Tuple[float, float] = (0, 0),
    size: float = 1.0,
    depth: int = 3
) -> List[Tuple[float, float]]:
    """
    Generate Koch snowflake (3 Koch curves).
    
    >>> points = koch_snowflake(depth=2)
    >>> len(points) > 3
    True
    """
    # Equilateral triangle vertices
    h = size * math.sqrt(3) / 2
    vertices = [
        (center[0], center[1] + h * 2/3),
        (center[0] - size/2, center[1] - h/3),
        (center[0] + size/2, center[1] - h/3)
    ]
    
    points = []
    for i in range(3):
        start = vertices[i]
        end = vertices[(i + 1) % 3]
        points.extend(koch_curve(start, end, depth)[:-1])
    
    return points


class LSystem:
    """
    L-System (Lindenmayer System) for generating fractals.
    
    >>> ls = LSystem("F", {"F": "F+F-F-F+F"})
    >>> result = ls.generate(2)
    >>> "F" in result
    True
    """
    
    def __init__(
        self,
        axiom: str,
        rules: Dict[str, str],
        angle: float = 90.0
    ):
        """
        Initialize L-System.
        
        Args:
            axiom: Starting string
            rules: Production rules
            angle: Turn angle in degrees
        """
        self.axiom = axiom
        self.rules = rules
        self.angle = math.radians(angle)
    
    def generate(self, iterations: int) -> str:
        """Generate L-System string."""
        current = self.axiom
        
        for _ in range(iterations):
            next_str = ""
            for char in current:
                if char in self.rules:
                    next_str += self.rules[char]
                else:
                    next_str += char
            current = next_str
        
        return current
    
    def interpret(
        self,
        instructions: str,
        length: float = 10.0
    ) -> List[Tuple[Tuple[float, float], Tuple[float, float]]]:
        """
        Interpret L-System string as turtle graphics.
        
        Returns list of line segments.
        """
        x, y = 0.0, 0.0
        direction = math.pi / 2  # Start pointing up
        stack = []
        lines = []
        
        for char in instructions:
            if char == 'F' or char == 'G':
                # Draw forward
                new_x = x + length * math.cos(direction)
                new_y = y + length * math.sin(direction)
                lines.append(((x, y), (new_x, new_y)))
                x, y = new_x, new_y
            elif char == 'f':
                # Move without drawing
                x += length * math.cos(direction)
                y += length * math.sin(direction)
            elif char == '+':
                direction += self.angle
            elif char == '-':
                direction -= self.angle
            elif char == '[':
                stack.append((x, y, direction))
            elif char == ']':
                x, y, direction = stack.pop()
        
        return lines
    
    # Common L-Systems
    @classmethod
    def dragon_curve(cls) -> 'LSystem':
        """Dragon curve L-System."""
        return cls("FX", {"X": "X+YF+", "Y": "-FX-Y"}, 90)
    
    @classmethod
    def sierpinski_triangle(cls) -> 'LSystem':
        """Sierpiński triangle L-System."""
        return cls("F-G-G", {"F": "F-G+F+G-F", "G": "GG"}, 120)
    
    @classmethod
    def hilbert_curve(cls) -> 'LSystem':
        """Hilbert curve L-System."""
        return cls("A", {
            "A": "-BF+AFA+FB-",
            "B": "+AF-BFB-FA+"
        }, 90)
    
    @classmethod
    def plant(cls) -> 'LSystem':
        """Plant-like structure."""
        return cls("X", {
            "X": "F+[[X]-X]-F[-FX]+X",
            "F": "FF"
        }, 25)


def barnsley_fern(
    iterations: int = 10000
) -> List[Tuple[float, float]]:
    """
    Generate Barnsley Fern using IFS.
    
    >>> points = barnsley_fern(100)
    >>> len(points)
    100
    """
    import random
    
    points = []
    x, y = 0.0, 0.0
    
    # Transformation probabilities and coefficients
    transforms = [
        (0.01, 0, 0, 0, 0.16, 0, 0),        # Stem
        (0.85, 0.85, 0.04, -0.04, 0.85, 0, 1.6),  # Main
        (0.07, 0.2, -0.26, 0.23, 0.22, 0, 1.6),   # Left
        (0.07, -0.15, 0.28, 0.26, 0.24, 0, 0.44)  # Right
    ]
    
    for _ in range(iterations):
        r = random.random()
        
        cumulative = 0
        for prob, a, b, c, d, e, f in transforms:
            cumulative += prob
            if r < cumulative:
                x_new = a * x + b * y + e
                y_new = c * x + d * y + f
                x, y = x_new, y_new
                break
        
        points.append((x, y))
    
    return points


def render_ascii(
    points: List[Tuple[float, float]],
    width: int = 60,
    height: int = 30
) -> str:
    """Render points as ASCII art."""
    if not points:
        return ""
    
    # Find bounds
    xs = [p[0] for p in points]
    ys = [p[1] for p in points]
    x_min, x_max = min(xs), max(xs)
    y_min, y_max = min(ys), max(ys)
    
    # Scale to grid
    grid = [[' ' for _ in range(width)] for _ in range(height)]
    
    for x, y in points:
        px = int((x - x_min) / (x_max - x_min + 1e-10) * (width - 1))
        py = int((y - y_min) / (y_max - y_min + 1e-10) * (height - 1))
        py = height - 1 - py  # Flip y-axis
        grid[py][px] = '█'
    
    return '\n'.join(''.join(row) for row in grid)


def render_mandelbrot_ascii(
    image: List[List[int]],
    max_iter: int
) -> str:
    """Render Mandelbrot as ASCII."""
    chars = " .:-=+*#%@"
    
    lines = []
    for row in image:
        line = ""
        for val in row:
            if val == max_iter:
                line += ' '
            else:
                idx = int(val / max_iter * (len(chars) - 1))
                line += chars[idx]
        lines.append(line)
    
    return '\n'.join(lines)


# Demo
if __name__ == "__main__":
    print("Fractal Algorithms Demo")
    print("=" * 60)
    
    # Mandelbrot
    print("\n1. Mandelbrot Set:")
    img = mandelbrot(60, 30, max_iter=50)
    print(render_mandelbrot_ascii(img, 50))
    
    # Julia Set
    print("\n2. Julia Set (c = -0.7 + 0.27i):")
    julia_img = julia_set(40, 20, complex(-0.7, 0.27), max_iter=30)
    print(render_mandelbrot_ascii(julia_img, 30))
    
    # Sierpiński
    print("\n3. Sierpiński Triangle (Chaos Game):")
    sierpinski = sierpinski_chaos_game(3000)
    print(render_ascii(sierpinski, 40, 20))
    
    # Koch Snowflake
    print("\n4. Koch Snowflake:")
    koch = koch_snowflake(depth=3)
    print(render_ascii(koch, 50, 25))
    
    # L-System Dragon
    print("\n5. Dragon Curve (L-System):")
    dragon = LSystem.dragon_curve()
    dragon_str = dragon.generate(10)
    dragon_lines = dragon.interpret(dragon_str, 5)
    dragon_points = [line[0] for line in dragon_lines]
    print(render_ascii(dragon_points, 50, 25))
    
    # Barnsley Fern
    print("\n6. Barnsley Fern:")
    fern = barnsley_fern(5000)
    print(render_ascii(fern, 30, 40))
```

## 8. Applications

### 8.1 Computer Graphics
- Procedural terrain generation
- Texture synthesis
- Realistic nature rendering

### 8.2 Scientific Modeling
- Coastline measurement
- Blood vessel networks
- Antenna design

### 8.3 Data Analysis
- Time series analysis
- Market behavior modeling
- Signal compression

## 9. Real-World Software Engineering Applications

### 9.1 Production Example: Terrain Generator

```python
from typing import Dict, List, Tuple, Optional
from dataclasses import dataclass
import numpy as np
import random
import math


@dataclass
class TerrainConfig:
    """Configuration for fractal terrain generation."""
    width: int = 256
    height: int = 256
    roughness: float = 0.5
    sea_level: float = 0.4
    seed: Optional[int] = None


class DiamondSquare:
    """
    Diamond-Square algorithm for fractal terrain.
    
    Creates realistic heightmaps for games and simulations.
    """
    
    def __init__(self, config: TerrainConfig):
        self.config = config
        
        # Size must be 2^n + 1
        size = 1
        while size + 1 < max(config.width, config.height):
            size *= 2
        self.size = size + 1
        
        self.heightmap = np.zeros((self.size, self.size), dtype=np.float32)
        
        if config.seed is not None:
            np.random.seed(config.seed)
            random.seed(config.seed)
    
    def generate(self) -> np.ndarray:
        """Generate terrain heightmap."""
        # Initialize corners
        self.heightmap[0, 0] = random.random()
        self.heightmap[0, -1] = random.random()
        self.heightmap[-1, 0] = random.random()
        self.heightmap[-1, -1] = random.random()
        
        step_size = self.size - 1
        scale = 1.0
        
        while step_size > 1:
            half = step_size // 2
            
            # Diamond step
            self._diamond_step(step_size, scale)
            
            # Square step
            self._square_step(step_size, scale)
            
            step_size //= 2
            scale *= self.config.roughness
        
        # Normalize to 0-1
        self.heightmap = (self.heightmap - self.heightmap.min()) / \
                         (self.heightmap.max() - self.heightmap.min())
        
        # Crop to requested size
        return self.heightmap[:self.config.height, :self.config.width]
    
    def _diamond_step(self, step_size: int, scale: float):
        """Diamond step: set center of each square."""
        half = step_size // 2
        
        for y in range(half, self.size - 1, step_size):
            for x in range(half, self.size - 1, step_size):
                avg = (
                    self.heightmap[y - half, x - half] +
                    self.heightmap[y - half, x + half] +
                    self.heightmap[y + half, x - half] +
                    self.heightmap[y + half, x + half]
                ) / 4.0
                
                self.heightmap[y, x] = avg + (random.random() - 0.5) * scale
    
    def _square_step(self, step_size: int, scale: float):
        """Square step: set center of each diamond."""
        half = step_size // 2
        
        for y in range(0, self.size, half):
            for x in range((y + half) % step_size, self.size, step_size):
                values = []
                
                if y >= half:
                    values.append(self.heightmap[y - half, x])
                if y + half < self.size:
                    values.append(self.heightmap[y + half, x])
                if x >= half:
                    values.append(self.heightmap[y, x - half])
                if x + half < self.size:
                    values.append(self.heightmap[y, x + half])
                
                if values:
                    avg = sum(values) / len(values)
                    self.heightmap[y, x] = avg + (random.random() - 0.5) * scale


class FractalNoiseGenerator:
    """
    Multi-octave fractal noise (fBm) generator.
    
    Uses layered Perlin-like noise for natural textures.
    """
    
    def __init__(
        self,
        octaves: int = 6,
        persistence: float = 0.5,
        lacunarity: float = 2.0,
        seed: Optional[int] = None
    ):
        self.octaves = octaves
        self.persistence = persistence
        self.lacunarity = lacunarity
        
        if seed is not None:
            random.seed(seed)
        
        # Generate permutation table
        self.perm = list(range(256))
        random.shuffle(self.perm)
        self.perm = self.perm + self.perm  # Double for overflow
    
    def _gradient(self, h: int, x: float, y: float) -> float:
        """Compute gradient dot product."""
        h = h & 3
        if h == 0:
            return x + y
        elif h == 1:
            return -x + y
        elif h == 2:
            return x - y
        else:
            return -x - y
    
    def _fade(self, t: float) -> float:
        """Smooth fade function."""
        return t * t * t * (t * (t * 6 - 15) + 10)
    
    def _lerp(self, a: float, b: float, t: float) -> float:
        """Linear interpolation."""
        return a + t * (b - a)
    
    def _noise2d(self, x: float, y: float) -> float:
        """2D Perlin-like noise."""
        xi = int(x) & 255
        yi = int(y) & 255
        xf = x - int(x)
        yf = y - int(y)
        
        u = self._fade(xf)
        v = self._fade(yf)
        
        aa = self.perm[self.perm[xi] + yi]
        ab = self.perm[self.perm[xi] + yi + 1]
        ba = self.perm[self.perm[xi + 1] + yi]
        bb = self.perm[self.perm[xi + 1] + yi + 1]
        
        x1 = self._lerp(
            self._gradient(aa, xf, yf),
            self._gradient(ba, xf - 1, yf),
            u
        )
        x2 = self._lerp(
            self._gradient(ab, xf, yf - 1),
            self._gradient(bb, xf - 1, yf - 1),
            u
        )
        
        return self._lerp(x1, x2, v)
    
    def fbm(self, x: float, y: float) -> float:
        """Fractional Brownian Motion."""
        total = 0.0
        amplitude = 1.0
        frequency = 1.0
        max_value = 0.0
        
        for _ in range(self.octaves):
            total += amplitude * self._noise2d(x * frequency, y * frequency)
            max_value += amplitude
            amplitude *= self.persistence
            frequency *= self.lacunarity
        
        return total / max_value
    
    def generate(
        self,
        width: int,
        height: int,
        scale: float = 50.0
    ) -> np.ndarray:
        """Generate noise heightmap."""
        heightmap = np.zeros((height, width), dtype=np.float32)
        
        for y in range(height):
            for x in range(width):
                heightmap[y, x] = self.fbm(x / scale, y / scale)
        
        # Normalize to 0-1
        heightmap = (heightmap + 1) / 2
        return heightmap


class ProceduralWorld:
    """
    Complete procedural world generator combining fractal techniques.
    """
    
    def __init__(self, width: int = 256, height: int = 256, seed: int = 42):
        self.width = width
        self.height = height
        self.seed = seed
        
        self.heightmap = None
        self.moisture = None
        self.temperature = None
        self.biomes = None
    
    def generate(self):
        """Generate complete world."""
        # Height using Diamond-Square
        ds_config = TerrainConfig(
            width=self.width,
            height=self.height,
            roughness=0.5,
            seed=self.seed
        )
        ds = DiamondSquare(ds_config)
        self.heightmap = ds.generate()
        
        # Moisture using fractal noise
        moisture_gen = FractalNoiseGenerator(
            octaves=4,
            persistence=0.5,
            seed=self.seed + 1
        )
        self.moisture = moisture_gen.generate(
            self.width, self.height, scale=80
        )
        
        # Temperature (based on latitude + noise)
        temp_gen = FractalNoiseGenerator(
            octaves=3,
            persistence=0.3,
            seed=self.seed + 2
        )
        temp_noise = temp_gen.generate(self.width, self.height, scale=100)
        
        # Latitude gradient
        self.temperature = np.zeros((self.height, self.width))
        for y in range(self.height):
            lat_temp = 1.0 - abs(y / self.height - 0.5) * 2
            self.temperature[y, :] = lat_temp * 0.7 + temp_noise[y, :] * 0.3
        
        # Assign biomes
        self._assign_biomes()
    
    def _assign_biomes(self):
        """Assign biome types based on height, moisture, temperature."""
        self.biomes = np.zeros((self.height, self.width), dtype=np.int8)
        
        for y in range(self.height):
            for x in range(self.width):
                h = self.heightmap[y, x]
                m = self.moisture[y, x]
                t = self.temperature[y, x]
                
                if h < 0.3:
                    self.biomes[y, x] = 0  # Ocean
                elif h < 0.35:
                    self.biomes[y, x] = 1  # Beach
                elif h > 0.8:
                    self.biomes[y, x] = 7  # Mountain
                elif h > 0.9:
                    self.biomes[y, x] = 8  # Snow
                elif t < 0.3:
                    if m > 0.5:
                        self.biomes[y, x] = 6  # Taiga
                    else:
                        self.biomes[y, x] = 5  # Tundra
                elif t > 0.7:
                    if m < 0.3:
                        self.biomes[y, x] = 4  # Desert
                    else:
                        self.biomes[y, x] = 3  # Rainforest
                else:
                    if m > 0.5:
                        self.biomes[y, x] = 2  # Forest
                    else:
                        self.biomes[y, x] = 9  # Grassland
    
    def render(self) -> str:
        """Render world as ASCII."""
        biome_chars = {
            0: '~',  # Ocean
            1: '.',  # Beach
            2: '♣',  # Forest
            3: '♠',  # Rainforest
            4: '░',  # Desert
            5: '▒',  # Tundra
            6: '▓',  # Taiga
            7: '▲',  # Mountain
            8: '△',  # Snow
            9: ',',  # Grassland
        }
        
        lines = []
        for row in self.biomes:
            line = ''.join(biome_chars[cell] for cell in row)
            lines.append(line)
        return '\n'.join(lines)
    
    def get_stats(self) -> Dict[str, float]:
        """Get world statistics."""
        total = self.width * self.height
        biome_names = [
            'ocean', 'beach', 'forest', 'rainforest', 
            'desert', 'tundra', 'taiga', 'mountain', 
            'snow', 'grassland'
        ]
        
        stats = {}
        for i, name in enumerate(biome_names):
            count = np.sum(self.biomes == i)
            stats[name] = count / total
        
        return stats


# Demo
if __name__ == "__main__":
    print("Procedural World Generator Demo")
    print("=" * 60)
    
    # Diamond-Square terrain
    print("\n1. Diamond-Square Terrain:")
    config = TerrainConfig(width=64, height=32, roughness=0.5, seed=42)
    ds = DiamondSquare(config)
    heightmap = ds.generate()
    
    # Render as ASCII
    chars = " .:-=+*#%@"
    for row in heightmap:
        line = ""
        for val in row:
            idx = int(val * (len(chars) - 1))
            line += chars[idx]
        print(line)
    
    # Fractal Noise
    print("\n2. Fractal Brownian Motion:")
    noise_gen = FractalNoiseGenerator(octaves=4, seed=42)
    noise = noise_gen.generate(64, 16, scale=20)
    
    for row in noise:
        line = ""
        for val in row:
            idx = int(val * (len(chars) - 1))
            line += chars[idx]
        print(line)
    
    # Complete World
    print("\n3. Procedural World:")
    world = ProceduralWorld(width=80, height=30, seed=42)
    world.generate()
    print(world.render())
    print(f"\n   Biome distribution: {world.get_stats()}")
```

## 10. Comparison

| Fractal | Complexity | Visual Appeal | Use Case |
|---------|------------|---------------|----------|
| Mandelbrot | High | Very High | Art, exploration |
| Julia | Medium | High | Animation, art |
| Sierpiński | Low | Medium | Education |
| Koch | Low | Medium | Education |
| L-Systems | Variable | High | Plants, patterns |
| Barnsley Fern | Low | High | Nature |
| Diamond-Square | Medium | High | Terrain |

## 11. Best Practices

1. **Use appropriate precision**: Double for deep zooms
2. **Optimize escape detection**: Cardioid/bulb check
3. **Parallelize computation**: Embarrassingly parallel
4. **Progressive rendering**: Show results early
5. **Cache intermediate results**: Especially for L-systems

## 12. References

- [Wikipedia: Fractal](https://en.wikipedia.org/wiki/Fractal)
- Mandelbrot, B. "The Fractal Geometry of Nature"
- [Fractal Foundation](https://fractalfoundation.org/)
- Prusinkiewicz, P. "The Algorithmic Beauty of Plants"
