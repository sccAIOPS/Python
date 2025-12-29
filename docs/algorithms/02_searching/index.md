# Searching Algorithms

This directory contains comprehensive documentation for searching algorithms implemented in the TheAlgorithms/Python repository.

## Overview

Searching algorithms are fundamental techniques for finding specific elements within data structures. They range from simple linear scans to sophisticated optimization methods.

## Algorithm Categories

### Basic Search Algorithms
| Algorithm | Time Complexity | Space | Best For |
|-----------|----------------|-------|----------|
| [Linear Search](linear_search.md) | O(n) | O(1) | Unsorted data |
| [Sentinel Linear Search](sentinel_linear_search.md) | O(n) | O(1) | Optimized linear |
| [Binary Search](binary_search.md) | O(log n) | O(1) | Sorted arrays |
| [Simple Binary Search](simple_binary_search.md) | O(log n) | O(1) | Basic sorted search |

### Advanced Search Algorithms
| Algorithm | Time Complexity | Space | Best For |
|-----------|----------------|-------|----------|
| [Jump Search](jump_search.md) | O(√n) | O(1) | Sorted, large arrays |
| [Interpolation Search](interpolation_search.md) | O(log log n) avg | O(1) | Uniform distribution |
| [Exponential Search](exponential_search.md) | O(log n) | O(1) | Unbounded arrays |
| [Fibonacci Search](fibonacci_search.md) | O(log n) | O(1) | Large sorted arrays |
| [Ternary Search](ternary_search.md) | O(log₃ n) | O(1) | Unimodal functions |

### Selection Algorithms
| Algorithm | Time Complexity | Space | Best For |
|-----------|----------------|-------|----------|
| [Quick Select](quick_select.md) | O(n) average | O(1) | Kth element |
| [Median of Medians](median_of_medians.md) | O(n) guaranteed | O(log n) | Deterministic kth |

### Tree Traversals
| Algorithm | Time Complexity | Space | Best For |
|-----------|----------------|-------|----------|
| [Binary Tree Traversal](binary_tree_traversal.md) | O(n) | O(h) | Tree exploration |

### Optimization Searches
| Algorithm | Type | Best For |
|-----------|------|----------|
| [Hill Climbing](hill_climbing.md) | Local Search | Continuous optimization |
| [Simulated Annealing](simulated_annealing.md) | Metaheuristic | Global optimization |
| [Tabu Search](tabu_search.md) | Metaheuristic | Combinatorial optimization |

### Recursive Variants
| Algorithm | Time Complexity | Space | Notes |
|-----------|----------------|-------|-------|
| [Double Linear Search](double_linear_search.md) | O(n) | O(1) | Two-pointer approach |
| [Double Linear Search Recursion](double_linear_search_recursion.md) | O(n) | O(n) | Recursive variant |

## Comparison Matrix

### Search Performance by Data Characteristics

| Scenario | Best Algorithm | Why |
|----------|----------------|-----|
| Unsorted small array | Linear Search | Simple, no preprocessing |
| Sorted array | Binary Search | O(log n) guaranteed |
| Uniformly distributed | Interpolation Search | O(log log n) average |
| Very large sorted | Jump Search | Balance of comparisons |
| Unknown size | Exponential Search | Doubles range efficiently |
| Find kth element | Quick Select | O(n) average |

### When to Use Each

```
Data Unsorted?
├── Yes → Linear Search
│   └── Optimization needed? → Sentinel Linear Search
└── No (Sorted)
    ├── Small array (< 100)? → Binary Search
    ├── Uniform distribution? → Interpolation Search
    ├── Unknown/infinite size? → Exponential Search
    ├── Need kth element? → Quick Select
    └── Large array? → Jump Search or Fibonacci Search
```

## Quick Reference

### Basic Searches
```python
# Linear Search - O(n)
def linear_search(arr, target):
    for i, val in enumerate(arr):
        if val == target:
            return i
    return -1

# Binary Search - O(log n)
def binary_search(arr, target):
    left, right = 0, len(arr) - 1
    while left <= right:
        mid = (left + right) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1
```

## Learning Path

### Beginner
1. [Linear Search](linear_search.md) - Foundation of searching
2. [Binary Search](binary_search.md) - Divide and conquer
3. [Jump Search](jump_search.md) - Block-based searching

### Intermediate
4. [Interpolation Search](interpolation_search.md) - Smart probing
5. [Exponential Search](exponential_search.md) - Range finding
6. [Quick Select](quick_select.md) - Selection algorithm

### Advanced
7. [Median of Medians](median_of_medians.md) - Deterministic selection
8. [Simulated Annealing](simulated_annealing.md) - Global optimization
9. [Tabu Search](tabu_search.md) - Combinatorial optimization

## See Also

- [Sorting Algorithms](../01_sorting/index.md) - Often used before searching
- [Data Structures](../06_data_structures/index.md) - Search-optimized structures
- [Graph Algorithms](../04_graphs/index.md) - Graph search algorithms
