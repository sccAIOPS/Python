# Code Style and Conventions

## Python Version
- **Target**: Python 3.14+ (as specified in pyproject.toml)
- Use modern Python features and syntax

## Naming Conventions (PEP 8)
- **Variables/Functions**: `snake_case` (e.g., `bubble_sort_iterative`, `generate_all_combinations`)
- **Classes**: `PascalCase` (e.g., `Node`, `LinkedList`)
- **Constants**: `UPPER_CASE` (e.g., `MAX_VALUE`)
- **File names**: `snake_case.py` (strictly enforced)
- **Descriptive names**: Avoid single-letter variables; expand acronyms
  - ❌ `gcd()` → ✅ `greatest_common_divisor()`

## Type Hints
- **Required** for all function parameters and return values
- Use modern type syntax (Python 3.10+):
  ```python
  def bubble_sort(collection: list[Any]) -> list[Any]:
  ```
- Use `|` for union types instead of `Union`
- Use `list[int]` instead of `List[int]`

## Docstrings
- **Required** for all functions and classes
- Format: Triple-quoted strings with clear explanations
- Include **doctests** in all docstrings
- Add source URLs if algorithm is from Wikipedia or other references

### Docstring Template
```python
def function_name(param: type) -> return_type:
    """
    Brief description of what the function does.

    :param param: Description of parameter
    :return: Description of return value

    Examples:
    >>> function_name(input)
    expected_output
    >>> function_name(edge_case)
    expected_edge_output
    """
```

## Doctests Requirements
- Test **valid inputs** with expected outputs
- Test **edge cases** (empty input, zero, negative)
- Test **error cases** with `Traceback` examples
- Compare with standard library when applicable:
  ```python
  >>> my_sort([3, 1, 2]) == sorted([3, 1, 2])
  True
  ```

## Error Handling
- Raise appropriate exceptions (`ValueError`, `TypeError`, etc.)
- Include descriptive error messages
- Document exceptions in doctests

## Code Formatting
- **Formatter**: Ruff (specified in pre-commit)
- **Linter**: Ruff with extensive rule set
- Line length: Default (88 characters from Ruff)
- Use f-strings for string formatting

## Class Style
- Use `@dataclass` decorator when appropriate
- Include `__repr__` method for debugging
- Use type hints for class attributes

### Class Example
```python
@dataclass
class Node:
    """
    Description with doctests.
    >>> Node(20)
    Node(20)
    """
    data: Any
    next_node: Node | None = None

    def __repr__(self) -> str:
        """String representation with doctests."""
        return f"Node({self.data})"
```

## Import Style
- Group imports: stdlib → third-party → local
- Sorted alphabetically (enforced by isort/ruff)
- Avoid external libraries for basic algorithms

## Algorithm Requirements
1. Take one or more inputs
2. Perform internal calculations (not just call libraries)
3. Return outputs (don't just print)
4. Minimal side effects
5. Add unique value beyond existing packages

## Don'ts
- Don't use `input()` for user interaction
- Don't use `print()` for results (return instead)
- Don't plagiarize code
- Don't submit identical implementations
- Don't create unnecessary new directories
