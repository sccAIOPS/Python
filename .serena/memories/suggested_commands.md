# Suggested Commands

## System Commands (Linux)
```bash
# Navigation and file operations
ls -la                    # List files with details
cd <directory>            # Change directory
pwd                       # Print working directory
find . -name "*.py"       # Find Python files
grep -r "pattern" .       # Search for pattern in files

# Git commands
git status                # Check repository status
git diff                  # Show unstaged changes
git add <file>            # Stage files
git commit -m "message"   # Commit changes
git push                  # Push to remote
git pull                  # Pull from remote
git checkout -b <branch>  # Create new branch
```

## Package Management (uv)
```bash
# Install dependencies
uv sync                           # Install all dependencies
uv sync --group test              # Install test dependencies
uv sync --group docs              # Install documentation dependencies

# Add new dependencies
uv add <package>                  # Add a package
uv add --dev <package>            # Add dev dependency
```

## Linting and Formatting

### Ruff (Primary linter/formatter)
```bash
# Check for issues
ruff check                        # Lint all files
ruff check <file.py>              # Lint specific file
ruff check --fix                  # Auto-fix issues

# Format code
ruff format                       # Format all files
ruff format <file.py>             # Format specific file
```

### Pre-commit (Recommended)
```bash
# Install pre-commit hooks (first time only)
pip install pre-commit
pre-commit install

# Run all hooks manually
pre-commit run --all-files --show-diff-on-failure
```

## Type Checking

### Mypy
```bash
# Check all files
mypy --ignore-missing-imports .

# Check specific file
mypy --ignore-missing-imports path/to/file.py
```

## Testing

### Pytest
```bash
# Run all tests
pytest

# Run with verbose output
pytest -v

# Run specific file
pytest path/to/test_file.py

# Run with coverage
pytest --cov
```

### Doctests
```bash
# Run doctests for a specific file
python3 -m doctest -v my_submission.py

# Run all doctests via pytest
pytest --doctest-modules
```

## Documentation

### Sphinx
```bash
# Build documentation
cd docs
sphinx-build -b html source _build/html
```

## Running Individual Algorithms
```bash
# Most files can be run directly
python3 path/to/algorithm.py

# This often runs the if __name__ == "__main__" block
# which may include performance benchmarks or demos
```

## Validation Scripts
```bash
# Validate filenames
./scripts/validate_filenames.py

# Validate Project Euler solutions
python scripts/validate_solutions.py
```

## Quick Development Workflow
```bash
# 1. Create/edit your algorithm file
# 2. Run doctests to verify
python3 -m doctest -v your_file.py

# 3. Run linting
ruff check your_file.py
ruff format your_file.py

# 4. Run type checking
mypy --ignore-missing-imports your_file.py

# 5. Run pre-commit hooks
pre-commit run --all-files
```
