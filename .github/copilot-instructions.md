# TheAlgorithms/Python - Copilot Standards

## Repository Profile
- **Type**: Educational Algorithms & Data Structures Library
- **Language**: Python 3.14+
- **Package Manager**: uv (pyproject.toml)
- **Linter/Formatter**: Ruff (extensive rule set)
- **Testing**: pytest + doctests (primary)
- **Documentation**: Sphinx with autoapi

## Topology: Star Architecture

```
                     ┌─────────────────────┐
                     │   @Plan (Planner)   │
                     │    Orchestrator     │
                     └──────────┬──────────┘
                                │
        ┌───────────────────────┼───────────────────────┐
        │           │           │           │           │
        ▼           ▼           ▼           ▼           ▼
┌───────────┐ ┌───────────┐ ┌───────────┐ ┌───────────┐ ┌───────────┐
│ @Architect│ │ @Designer │ │  @TDD     │ │ @Reviewer │ │  @Tester  │
│  Analyst  │ │  System   │ │Implementer│ │   Code    │ │  Quality  │
└───────────┘ └───────────┘ └───────────┘ └───────────┘ └───────────┘
```

**Rationale**: Star topology selected because this is a single-module educational repository. Each agent has a specialized focus, coordinated through the central Planner agent.

---

## Active Agents

| Agent | Responsibility | When to Use |
|-------|---------------|-------------|
| `@Plan` | Multi-step planning & orchestration | Complex tasks requiring multiple phases |
| `@ArchitectureAnalyst` | Extract architecture, generate UML/PlantUML diagrams | Understanding codebase structure |
| `@PatternExtractor` | Extract design patterns, data structures, algorithms | Analyzing implementation patterns |
| `@AlgorithmAnalyst` | Analyze algorithm complexity, pitfalls, recommendations | Deep algorithm analysis |
| `@RequirementsPlanner` | BDD/SOLID planning, feature specification | Planning new features |
| `@SystemDesigner` | High/low-level system design | Designing new components |
| `@TDDImplementer` | Implement with TDD, unit & integration tests | Code implementation |
| `@CodeReviewer` | Code review, style check, security audit | Pre-merge review |
| `@TestEngineer` | API testing, load testing, NFR validation | Quality assurance |

---

## Workflow Rules

### 1. Context Awareness
- Always verify against `pyproject.toml` for Python version (3.14+), dependencies, and Ruff rules
- Check existing implementations in the 47+ algorithm directories before creating new files
- Reference `CONTRIBUTING.md` for code style requirements

### 2. Code Style Enforcement
- **Naming**: `snake_case` for files/functions, `PascalCase` for classes
- **Type Hints**: Required for all parameters and return values
- **Docstrings**: Required with doctests for all public functions
- **Linting**: Must pass `ruff check` before submission

### 3. Algorithm Standards
- Each algorithm should be a pure function (same input → same output)
- Return results instead of printing
- Include Wikipedia/source URLs in docstrings
- Test valid inputs, edge cases, and error cases in doctests

### 4. Testing Requirements
- Primary testing via doctests in function docstrings
- Run `python -m doctest -v file.py` locally
- pytest for complex test scenarios
- Compare with standard library when applicable

---

## Quick Reference Commands

```bash
# Install dependencies
uv sync

# Run linting
ruff check

# Run all tests
pytest

# Run specific doctest
python -m doctest -v path/to/file.py

# Pre-commit checks
pre-commit run --all-files
```

---

## Agent Invocation Examples

### Architecture Analysis
```
@ArchitectureAnalyst Generate C4 component diagram for the data_structures/ module
```

### Algorithm Analysis
```
@AlgorithmAnalyst Analyze the time complexity and potential pitfalls of sorts/quick_sort.py
```

### Feature Planning
```
@RequirementsPlanner Plan a new Red-Black Tree implementation using BDD approach
```

### Implementation
```
@TDDImplementer Implement the Red-Black Tree based on the plan from @RequirementsPlanner
```

### Code Review
```
@CodeReviewer Review the Red-Black Tree implementation for style, quality, and security
```

---

## Domain Prompts

The following domain-specific prompts are available in `.github/prompts/`:

| Prompt | Purpose |
|--------|---------|
| [python-algorithms.prompt.md](prompts/python-algorithms.prompt.md) | Python algorithm implementation patterns |
| [datastructures-patterns.prompt.md](prompts/datastructures-patterns.prompt.md) | Data structure implementation patterns |
| [testing-quality.prompt.md](prompts/testing-quality.prompt.md) | Testing and quality assurance patterns |

---

## Development Lifecycle

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   ANALYZE   │────►│    PLAN     │────►│   DESIGN    │
│             │     │             │     │             │
│@Architect   │     │@Requirements│     │@System      │
│@Pattern     │     │  Planner    │     │  Designer   │
│@Algorithm   │     │             │     │             │
└─────────────┘     └─────────────┘     └─────────────┘
                                               │
                                               ▼
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   RELEASE   │◄────│   REVIEW    │◄────│  IMPLEMENT  │
│             │     │             │     │             │
│@Test        │     │@CodeReviewer│     │@TDD         │
│  Engineer   │     │             │     │  Implementer│
└─────────────┘     └─────────────┘     └─────────────┘
```
