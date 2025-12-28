# GitHub Copilot Agents - User Guide

## Overview

This directory contains specialized GitHub Copilot agents for the TheAlgorithms/Python repository. Each agent is designed for a specific phase of the software development lifecycle.

## Architecture: Star Topology

```
                     ┌─────────────────────┐
                     │       @Plan         │
                     │   (Orchestrator)    │
                     └──────────┬──────────┘
                                │
        ┌───────────────────────┼───────────────────────┐
        │           │           │           │           │
        ▼           ▼           ▼           ▼           ▼
┌───────────┐ ┌───────────┐ ┌───────────┐ ┌───────────┐ ┌───────────┐
│@Architect │ │ @Pattern  │ │@Algorithm │ │@Requiremt │ │ @System   │
│ Analyst   │ │ Extractor │ │ Analyst   │ │ Planner   │ │ Designer  │
└───────────┘ └───────────┘ └───────────┘ └───────────┘ └───────────┘
                                │
        ┌───────────────────────┼───────────────────────┐
        │                       │                       │
        ▼                       ▼                       ▼
┌───────────────┐     ┌───────────────┐     ┌───────────────┐
│     @TDD      │     │    @Code      │     │    @Test      │
│  Implementer  │     │   Reviewer    │     │   Engineer    │
└───────────────┘     └───────────────┘     └───────────────┘
```

## Available Agents

### Analysis Phase

| Agent | File | Purpose |
|-------|------|---------|
| `@ArchitectureAnalyst` | [ArchitectureAnalyst.agent.md](ArchitectureAnalyst.agent.md) | Extract architecture, generate UML diagrams |
| `@PatternExtractor` | [PatternExtractor.agent.md](PatternExtractor.agent.md) | Identify design patterns, data structures |
| `@AlgorithmAnalyst` | [AlgorithmAnalyst.agent.md](AlgorithmAnalyst.agent.md) | Analyze complexity, pitfalls, optimizations |

### Planning Phase

| Agent | File | Purpose |
|-------|------|---------|
| `@RequirementsPlanner` | [RequirementsPlanner.agent.md](RequirementsPlanner.agent.md) | BDD/SOLID planning, requirement specs |
| `@SystemDesigner` | [SystemDesigner.agent.md](SystemDesigner.agent.md) | High/low-level system design |

### Implementation Phase

| Agent | File | Purpose |
|-------|------|---------|
| `@TDDImplementer` | [TDDImplementer.agent.md](TDDImplementer.agent.md) | TDD implementation, unit tests |

### Quality Phase

| Agent | File | Purpose |
|-------|------|---------|
| `@CodeReviewer` | [CodeReviewer.agent.md](CodeReviewer.agent.md) | Style, quality, security review |
| `@TestEngineer` | [TestEngineer.agent.md](TestEngineer.agent.md) | Functional, load, performance testing |

---

## How to Use Agents

### Invoking an Agent

In your Copilot chat, use the `@` symbol followed by the agent name:

```
@ArchitectureAnalyst Generate a C4 component diagram for data_structures/
```

### Chaining Agents

For complex tasks, chain agents in sequence:

```
1. @RequirementsPlanner Plan a new AVL Tree implementation
2. @SystemDesigner Design the AVL Tree based on requirements
3. @TDDImplementer Implement the AVL Tree with TDD
4. @CodeReviewer Review the implementation
5. @TestEngineer Run comprehensive tests
```

### Using with @Plan Orchestrator

For multi-step tasks, start with `@Plan`:

```
@Plan I need to add a Red-Black Tree to the data structures module
```

The Plan agent will orchestrate the appropriate specialists.

---

## Workflow Examples

### Example 1: Analyze Existing Code

```
User: @ArchitectureAnalyst Analyze the graphs/ module structure

Agent Output:
- C4 Component Diagram (PlantUML)
- Module dependency map
- Class relationships
```

### Example 2: Add New Algorithm

```
Step 1:
User: @RequirementsPlanner Plan a Trie data structure implementation

Step 2:
User: @SystemDesigner Design the Trie based on the requirements

Step 3:
User: @TDDImplementer Implement data_structures/trie/trie.py

Step 4:
User: @CodeReviewer Review the Trie implementation

Step 5:
User: @TestEngineer Run performance tests on Trie operations
```

### Example 3: Deep Algorithm Analysis

```
User: @AlgorithmAnalyst Analyze sorts/quick_sort.py for:
- Time/space complexity
- Potential pitfalls
- Optimization recommendations
```

### Example 4: Code Review Before PR

```
User: @CodeReviewer Review my changes in:
- data_structures/binary_tree/avl_tree.py
- Check style compliance
- Security scan
- Generate review report
```

---

## Agent Outputs

### PlantUML Diagrams

Agents generate PlantUML diagrams that can be rendered:

1. Copy the `@startuml ... @enduml` block
2. Paste into [PlantUML Online Server](http://www.plantuml.com/plantuml)
3. Or use VS Code PlantUML extension

### Test Reports

Test reports are in Markdown format with:
- Summary tables
- Detailed results
- Pass/fail status
- Recommendations

### Code Templates

Implementation agents provide ready-to-use code with:
- Type hints
- Docstrings with doctests
- Proper formatting

---

## Best Practices

### 1. Be Specific
```
❌ "Analyze the code"
✅ "@AlgorithmAnalyst Analyze time complexity of searches/binary_search.py"
```

### 2. Provide Context
```
✅ "@RequirementsPlanner Plan a B-Tree implementation for the data_structures/
    module following existing patterns in binary_tree/"
```

### 3. Chain for Complex Tasks
```
✅ Use @Plan for orchestration
✅ Use specific agents for focused work
```

### 4. Verify Outputs
```
✅ Run suggested commands (ruff, pytest, doctest)
✅ Check PlantUML diagrams render correctly
✅ Review generated code before committing
```

---

## Troubleshooting

### Agent Not Responding
- Ensure you're using exact agent name with `@`
- Check if agent file exists in `.github/agents/`

### Incorrect Output
- Provide more specific instructions
- Reference specific files or directories

### Missing Diagrams
- Copy full `@startuml...@enduml` block
- Use PlantUML renderer

---

## Contributing to Agents

To modify or add agents:

1. Follow the template in existing `.agent.md` files
2. Include YAML frontmatter with name, description, tools
3. Document capabilities and examples
4. Update this INSTRUCTION.md

---

## Quick Reference

| Task | Agent | Example |
|------|-------|---------|
| Architecture docs | `@ArchitectureAnalyst` | Generate C4 diagram |
| Find patterns | `@PatternExtractor` | List design patterns used |
| Complexity analysis | `@AlgorithmAnalyst` | Analyze O(n) for sort |
| Plan feature | `@RequirementsPlanner` | BDD for new structure |
| Design system | `@SystemDesigner` | Class diagram for tree |
| Implement | `@TDDImplementer` | Code with doctests |
| Review | `@CodeReviewer` | Style + security check |
| Test | `@TestEngineer` | Load test algorithm |
| Orchestrate | `@Plan` | Multi-step workflow |
