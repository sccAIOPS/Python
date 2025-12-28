# Tool Configuration Reference

## Ruff Configuration (pyproject.toml)

### Target Version
```toml
[tool.ruff]
target-version = "py314"
```

### Enabled Rule Sets
- **A** - flake8-builtins
- **ARG** - flake8-unused-arguments
- **ASYNC** - flake8-async
- **B** - flake8-bugbear
- **BLE** - flake8-blind-except
- **C4** - flake8-comprehensions
- **C90** - McCabe cyclomatic complexity
- **DJ** - flake8-django
- **DTZ** - flake8-datetimez
- **E** - pycodestyle errors
- **EM** - flake8-errmsg
- **EXE** - flake8-executable
- **F** - Pyflakes
- **FA** - flake8-future-annotations
- **FLY** - flynt
- **G** - flake8-logging-format
- **I** - isort
- **ICN** - flake8-import-conventions
- **INP** - flake8-no-pep420
- **INT** - flake8-gettext
- **ISC** - flake8-implicit-str-concat
- **N** - pep8-naming
- **NPY** - NumPy-specific rules
- **PD** - pandas-vet
- **PGH** - pygrep-hooks
- **PIE** - flake8-pie
- **PL** - Pylint
- **PT** - flake8-pytest-style
- **PYI** - flake8-pyi
- **RSE** - flake8-raise
- **RUF** - Ruff-specific rules
- **S** - flake8-bandit (security)
- **SIM** - flake8-simplify
- **SLF** - flake8-self
- **T10** - flake8-debugger
- **TD** - flake8-todos
- **TID** - flake8-tidy-imports
- **UP** - pyupgrade
- **W** - pycodestyle warnings
- **YTT** - flake8-2020

### Complexity Limits
- `max-complexity`: 17 (default 10)
- `max-args`: 10 (default 5)
- `max-branches`: 20 (default 12)
- `max-returns`: 8 (default 6)
- `max-statements`: 88 (default 50)

## Pre-commit Hooks

### Active Hooks
1. **pre-commit-hooks**
   - check-executables-have-shebangs
   - check-toml
   - check-yaml
   - end-of-file-fixer
   - trailing-whitespace
   - requirements-txt-fixer

2. **auto-walrus** - Converts to walrus operator where applicable

3. **ruff-pre-commit**
   - ruff-check
   - ruff-format

4. **codespell** - Spell checking

5. **pyproject-fmt** - Format pyproject.toml

6. **validate-filenames** (local) - Custom snake_case validation

7. **validate-pyproject** - Validate pyproject.toml

8. **mypy** - Type checking with flags:
   - --explicit-package-bases
   - --ignore-missing-imports
   - --install-types
   - --non-interactive

9. **prettier** - Format TOML/YAML files

## Pytest Configuration

```toml
[tool.pytest.ini_options]
markers = ["mat_ops: mark a test as utilizing matrix operations."]
addopts = [
    "--durations=10",
    "--doctest-modules",
    "--showlocals",
]
```

## Codespell Configuration

```toml
[tool.codespell]
ignore-words-list = "3rt,abd,aer,ans,bitap,..."
skip = "./.*,*.json,*.lock,..."
```

## Coverage Configuration

```toml
[tool.coverage.report]
omit = [".env/*", "project_euler/*"]
sort = "Cover"
```

## Mypy Flags (via pre-commit)
- `--explicit-package-bases`
- `--ignore-missing-imports`
- `--install-types`
- `--non-interactive`

## IDE Integration

### VS Code Recommended Extensions
- Python (Microsoft)
- Ruff (astral-sh)
- Mypy Type Checker

### Recommended Settings
```json
{
    "python.defaultInterpreterPath": ".venv/bin/python",
    "[python]": {
        "editor.defaultFormatter": "charliermarsh.ruff",
        "editor.formatOnSave": true
    },
    "ruff.lint.run": "onSave"
}
```
