# Domain-Specific Prompts - User Guide

## Overview

This directory contains domain-specific expertise prompts that can be used to enhance agent capabilities or provide specialized guidance for particular algorithm categories.

## Available Prompts

| Prompt | File | Domain |
|--------|------|--------|
| Python Algorithm Expertise | [python-algorithms.prompt.md](python-algorithms.prompt.md) | Python algorithm best practices |
| Data Structure Expertise | [datastructures-patterns.prompt.md](datastructures-patterns.prompt.md) | DS implementation patterns |
| Testing Expertise | [testing-quality.prompt.md](testing-quality.prompt.md) | Testing and quality patterns |

## How to Use

### With Agents

Reference prompts in agent instructions:

```
@TDDImplementer Using patterns from testing-quality.prompt.md, implement tests for...
```

### Standalone

Use prompts directly for focused guidance:

```
Following the patterns in python-algorithms.prompt.md, how should I structure...
```

## Prompt Categories

### 1. Language-Specific
- Python conventions and idioms
- Type hint best practices
- Docstring patterns

### 2. Domain-Specific
- Algorithm implementation patterns
- Data structure conventions
- Testing methodologies

### 3. Quality-Specific
- Code review checklists
- Security patterns
- Performance guidelines
