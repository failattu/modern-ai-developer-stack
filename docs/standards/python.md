# Python Service Standards

Opinionated guidelines for building Python services and tools that align with the "Modern AI Developer Stack" in this repo. These apply to new subprojects created under their own directory (not at the repo root).

---

## 1. Project Layout

- Create a subdirectory per service, for example `services/llm-api`.
- Use this basic layout:

```text
services/my-python-service/
  pyproject.toml
  src/
    my_python_service/
      __init__.py
      main.py
  tests/
    test_main.py
```

- Keep importable code under `src/<package_name>`.
- Use `tests/` for unit and integration tests; mirror the package structure when it grows.

---

## 2. Tooling & Dependencies

- Prefer `uv` or `pip` + `venv` for dependency management.
- Use `pyproject.toml` for configuration when possible.
- Aim to pin direct dependencies and keep dev tooling (pytest, ruff, mypy) as dev dependencies.

Example `pyproject.toml` skeleton:

```toml
[project]
name = "my-python-service"
version = "0.1.0"
requires-python = ">=3.10"

[project.optional-dependencies]
dev = [
  "pytest",
  "ruff",
  "mypy"
]

[tool.pytest.ini_options]
addopts = "-q"

[tool.ruff]
line-length = 100

[tool.mypy]
python_version = "3.10"
strict = true
```

---

## 3. Formatting & Linting

- Use `ruff format` (or `black`) for formatting.
- Use `ruff check .` for linting.
- Keep line length at or below 100–120 characters.

Common commands:

```bash
ruff format .
ruff check .
```

- Run formatting and linting in CI to keep the codebase consistent.

---

## 4. Typing & Static Analysis

- Target modern Python (3.10+) and use type hints consistently.
- Enable strict typing where feasible:

```bash
mypy .
# or
pyright
```

- Add types for public APIs first, then expand coverage.
- Prefer explicit return types for functions that form a public surface (APIs, CLI entry points, libraries).

---

## 5. Testing Practices

- Use `pytest` as the default test runner.
- Keep tests deterministic; avoid external network or filesystem dependencies unless they are explicitly mocked.
- Match test module names to the modules under test, for example:
  - `src/my_python_service/main.py`
  - `tests/test_main.py`

Common commands:

```bash
pytest                                   # run all tests
pytest tests/test_main.py::test_happy_path  # single test
```

- Use fixtures to share setup logic and keep tests small and focused.

---

## 6. Imports & Modules

- Use absolute imports within the package:

```python
from my_python_service.config import load_config
```

- Avoid deeply nested relative imports where possible.
- Group imports in this order with blank lines between groups:
  1. Standard library.
  2. Third-party packages.
  3. Local application imports.

```python
import logging
from typing import Any

import httpx

from my_python_service.config import load_config
```

---

## 7. Error Handling

- Raise specific exceptions instead of returning `None` or sentinel values when things go wrong.
- Use custom exception types when a built-in type does not describe the problem well:

```python
class ConfigError(Exception):
    """Raised when configuration is invalid or missing."""

```

- Include actionable information in exception messages.
- For CLIs, exit with a non-zero status code on failure and print concise error messages to stderr.

---

## 8. Async & I/O

- Prefer async I/O libraries (`httpx`, `asyncio`, `aiofiles`) where concurrency matters.
- Avoid mixing blocking I/O into async code; if necessary, offload to a thread pool.
- Always await async calls and handle exceptions close to where they can occur.

---

## 9. Command Cheat Sheet

- `uv run pytest` or `pytest` – run all tests.
- `pytest path/to/test_file.py::test_case` – run a single test.
- `ruff format .` – format the codebase.
- `ruff check .` – lint the codebase.
- `mypy .` – run strict type checking.
