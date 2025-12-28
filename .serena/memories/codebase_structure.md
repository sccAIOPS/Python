# Codebase Structure Reference

## Root Directory
```
Python/
├── pyproject.toml          # Project configuration, dependencies, tool settings
├── uv.lock                 # Lock file for uv package manager
├── .pre-commit-config.yaml # Pre-commit hooks configuration
├── README.md               # Project readme (auto-generated)
├── DIRECTORY.md            # Algorithm directory listing (auto-generated)
├── CONTRIBUTING.md         # Contribution guidelines
├── LICENSE.md              # MIT License
├── index.md                # Documentation index
└── [47+ algorithm directories]
```

## Algorithm Directories (Alphabetical)

| Directory | Description | Notable Files |
|-----------|-------------|---------------|
| `audio_filters/` | Audio signal processing | butterworth_filter.py, iir_filter.py |
| `backtracking/` | Backtracking algorithms | n_queens.py, sudoku.py, knight_tour.py |
| `bit_manipulation/` | Bit operations | binary_shifts.py, is_power_of_two.py |
| `blockchain/` | Blockchain basics | diophantine_equation.py |
| `boolean_algebra/` | Logic gates | and_gate.py, imply_gate.py |
| `cellular_automata/` | CA simulations | - |
| `ciphers/` | Cryptography | caesar_cipher.py, rsa_cipher.py |
| `computer_vision/` | CV algorithms | - |
| `conversions/` | Unit/base conversions | binary_to_decimal.py |
| `data_compression/` | Compression algorithms | huffman.py |
| `data_structures/` | Data structure implementations | See subdirs below |
| `digital_image_processing/` | Image processing | - |
| `divide_and_conquer/` | D&C algorithms | merge_sort.py |
| `dynamic_programming/` | DP solutions | knapsack.py, fibonacci.py |
| `electronics/` | Electronics calculations | - |
| `file_transfer/` | File transfer protocols | - |
| `financial/` | Financial calculations | - |
| `fractals/` | Fractal generation | mandelbrot.py |
| `fuzzy_logic/` | Fuzzy logic systems | - |
| `genetic_algorithm/` | GA implementations | - |
| `geodesy/` | Geographic calculations | - |
| `geometry/` | Geometric algorithms | - |
| `graphics/` | Graphics algorithms | - |
| `graphs/` | Graph algorithms | dijkstra.py, bfs.py, dfs.py |
| `greedy_methods/` | Greedy algorithms | - |
| `hashes/` | Hash functions | md5.py, sha1.py |
| `knapsack/` | Knapsack problem variants | - |
| `linear_algebra/` | Linear algebra | - |
| `linear_programming/` | LP algorithms | - |
| `machine_learning/` | ML from scratch | knn.py, decision_tree.py |
| `maths/` | Mathematical functions | factorial.py, fibonacci.py, primes |
| `matrix/` | Matrix operations | - |
| `networking_flow/` | Network flow algorithms | - |
| `neural_network/` | Neural networks | perceptron.py |
| `other/` | Miscellaneous | - |
| `physics/` | Physics calculations | - |
| `project_euler/` | Project Euler solutions | problem_001/ through problem_XXX/ |
| `quantum/` | Quantum computing | - |
| `scheduling/` | Scheduling algorithms | - |
| `searches/` | Search algorithms | binary_search.py, linear_search.py |
| `sorts/` | Sorting algorithms | bubble_sort.py, quick_sort.py |
| `strings/` | String algorithms | kmp.py, rabin_karp.py |
| `web_programming/` | Web utilities | - |

## data_structures/ Subdirectories
```
data_structures/
├── arrays/
├── binary_tree/
├── disjoint_set/
├── hashing/
├── heap/
├── kd_tree/
├── linked_list/
├── queues/
├── stacks/
├── suffix_tree/
└── trie/
```

## maths/ Subdirectories
```
maths/
├── images/
├── numerical_analysis/
├── polynomials/
├── series/
└── special_numbers/
```

## scripts/ Directory
```
scripts/
├── build_directory_md.py          # Builds DIRECTORY.md
├── validate_filenames.py          # Validates snake_case naming
├── validate_solutions.py          # Validates Project Euler solutions
├── project_euler_answers.json     # Expected PE answers
└── close_pull_requests_*.sh       # PR automation scripts
```

## docs/ Directory
```
docs/
├── conf.py                        # Sphinx configuration
└── source/                        # Documentation source
```

## Configuration Files
- `pyproject.toml` - Primary config for:
  - Project metadata
  - Dependencies
  - Ruff linter/formatter settings
  - Pytest configuration
  - Mypy settings (via pre-commit)
  - Sphinx documentation settings
  
- `.pre-commit-config.yaml` - Pre-commit hooks

## Key Patterns

### Typical Algorithm File Structure
```python
"""
Module docstring with description.
Reference: https://en.wikipedia.org/wiki/Algorithm_Name
"""

def algorithm_function(param: type) -> return_type:
    """
    Function description.
    
    >>> algorithm_function(test_input)
    expected_output
    """
    # Implementation
    return result

if __name__ == "__main__":
    # Optional: benchmarks or demo
    import doctest
    doctest.testmod()
```

### Typical Data Structure File Structure
```python
from dataclasses import dataclass
from typing import Any

@dataclass
class Node:
    """Node class with doctests."""
    data: Any
    next: Node | None = None

class DataStructure:
    """Main data structure class."""
    
    def __init__(self) -> None:
        """Initialize with doctests."""
        pass
    
    def method(self, param: type) -> return_type:
        """Method with doctests."""
        pass

if __name__ == "__main__":
    # Demo or tests
    pass
```
