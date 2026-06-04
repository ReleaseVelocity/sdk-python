# Docker environment — strands-agents (sdk-python)

Containerized development environment for the Strands Agents Python SDK.

## What this provides
- **Base image:** `python:3.12-slim` (project requires Python >=3.10).
- **Install mode:** editable (`pip install -e ".[all,dev]"`) — installs the package in
  development mode with **all** runtime extras (a2a, anthropic, gemini, litellm,
  llamaapi, mistral, ollama, openai, writer, sagemaker, otel, docs) plus the
  **dev/test** toolchain (pytest, pytest-cov, pytest-asyncio, pytest-xdist, moto,
  mypy, ruff, hatch, pre-commit, commitizen).
- **Versioning:** the project uses `hatch-vcs` (version from git tags). The build
  context has no `.git`, so `SETUPTOOLS_SCM_PRETEND_VERSION` is provided via the
  `PKG_VERSION` build arg (default `1.0.0`).
- **System libs:** `git`, `build-essential`, `portaudio19-dev` for native deps.

## Usage
```bash
cd phantom-stack
sudo docker-compose up -d --build

# Collect tests (success criteria)
sudo docker-compose exec sdk-python python -m pytest --co -q

# Run the test suite (parallel)
sudo docker-compose exec sdk-python python -m pytest -n auto

# Lint / type-check
sudo docker-compose exec sdk-python ruff check
sudo docker-compose exec sdk-python mypy ./src

# Interactive shell
sudo docker-compose exec sdk-python bash
```

## Verification performed
- `python -m pytest --co -q` → **1705 tests collected**.
- `tests/strands/types` + `tests/strands/tools` → **467 passed** (with `-n auto`).
- `import strands` / `from strands import Agent` → OK.
- `ruff`, `mypy`, `pytest` all available.
