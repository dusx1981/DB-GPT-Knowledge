# AGENTS.md - Guidelines for AI Coding Agents

This document provides guidelines for AI coding agents working on the DB-GPT repository.

## Project Overview

DB-GPT is a Python monorepo using UV for package management. It consists of multiple packages in the `packages/` directory, with the main package being `dbgpt-core`.

## Build/Test/Lint Commands

### Setup (Required First)
```bash
# Install dependencies (run once or when dependencies change)
uv sync --all-packages \
  --extra "base" \
  --extra "proxy_openai" \
  --extra "rag" \
  --extra "storage_chromadb" \
  --extra "dbgpts"

# Activate virtual environment
source .venv/bin/activate
```

### Running Tests

**Run all tests:**
```bash
make test
# OR directly:
pytest --pyargs dbgpt
```

**Run a single test file:**
```bash
pytest packages/dbgpt-core/src/dbgpt/model/utils/tests/test_token_utils.py
```

**Run a specific test:**
```bash
pytest packages/dbgpt-core/src/dbgpt/model/utils/tests/test_token_utils.py::TestLRUTokenCache::test_init -v
```

**Run tests with coverage:**
```bash
make coverage
```

**Run doctests:**
```bash
make test-doc
```

### Linting and Formatting

**Format code (run before committing):**
```bash
make fmt
```

**Check formatting without changes:**
```bash
make fmt-check
```

**Run all pre-commit checks:**
```bash
make pre-commit
```

### Type Checking

**Run mypy:**
```bash
make mypy
```

Note: Type checking is currently limited to `packages/dbgpt-core/`.

## Code Style Guidelines

### Imports
- Use `from __future__ import annotations` at the top of files
- Group imports in this order:
  1. Standard library imports
  2. Third-party imports
  3. First-party imports (from dbgpt.*)
- Use absolute imports for first-party code: `from dbgpt.util import AppConfig`
- Use `TYPE_CHECKING` for imports only needed during type checking

### Formatting
- Line length: 88 characters (Black-compatible)
- Use double quotes for strings
- Use spaces for indentation
- Target Python version: 3.10+
- Ruff is used for formatting and import sorting

### Naming Conventions
- **Classes**: PascalCase (e.g., `LifeCycle`, `LRUTokenCache`)
- **Functions/Methods**: snake_case (e.g., `on_init`, `get_token_count`)
- **Variables**: snake_case (e.g., `max_size`, `current_memory`)
- **Constants**: UPPER_CASE (e.g., `MAX_RETRIES`)
- **Private members**: Prefix with underscore (e.g., `_internal_cache`)

### Type Hints
- Use type hints for function parameters and return types
- Use `Optional[Type]` for nullable values
- Use `TypeVar` for generic types when appropriate
- Use `from __future__ import annotations` to avoid circular imports
- Example:
  ```python
  from typing import Optional, TypeVar
  
  T = TypeVar("T")
  
  def get_component(name: str) -> Optional[Component]:
      pass
  ```

### Error Handling
- Use specific exceptions rather than generic `Exception`
- Use `try/except` blocks with specific exception types
- Log errors appropriately using the logging module
- Example:
  ```python
  import logging
  
  logger = logging.getLogger(__name__)
  
  try:
      result = process_data()
  except ValueError as e:
      logger.error(f"Invalid data: {e}")
      raise
  ```

### Documentation
- Use docstrings for all public classes and methods
- Follow Google-style docstrings
- Include type information in docstrings when not using type hints
- Example:
  ```python
  def process_items(items: list[str]) -> dict[str, int]:
      """Process a list of items and return counts.
      
      Args:
          items: List of item strings to process.
          
      Returns:
          Dictionary mapping items to their counts.
      """
      return {item: items.count(item) for item in set(items)}
  ```

### Testing
- Test files should be named `test_*.py`
- Test classes should use PascalCase (e.g., `TestLRUTokenCache`)
- Test methods should use snake_case starting with `test_`
- Use pytest fixtures when appropriate
- Include docstrings for test classes and methods
- Tests are located in `tests/` subdirectories within packages

### Package Structure
- Source code is in `packages/<package-name>/src/`
- Tests are co-located with source code in `tests/` subdirectories
- Each package has its own `pyproject.toml`
- First-party packages include: `dbgpt`, `dbgpt_acc_auto`, `dbgpt_client`, `dbgpt_ext`, `dbgpt_serve`, `dbgpt_app`, `dbgpt_sandbox`

## Pre-commit Checklist

Before committing code:
1. Run `make fmt` to format code
2. Run `make fmt-check` to verify formatting
3. Run `make test` to ensure tests pass
4. Run `make mypy` if type hints were modified

## Important Notes

- Python version requirement: >= 3.10
- Virtual environment is managed by UV in `.venv/`
- Ruff handles both linting and import sorting
- Mypy type checking is currently limited to dbgpt-core
- Excluded paths: `examples/notebook`, `*/tests/*`
