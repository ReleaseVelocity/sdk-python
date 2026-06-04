# Docker Dev Environment — Strands Agents Python SDK

Containerized development environment for the `strands-agents` SDK
(`/workspace/src/sdk-python`), with **all** runtime, optional, and dev/test
dependencies installed in editable mode.

## What's inside
- Base image: `python:3.12-slim` (project requires Python >=3.10; host had 3.9)
- Project installed via `pip install -e ".[all,dev]"` — editable install with
  every optional model-provider extra (`all`) plus the `dev` toolchain
  (pytest, pytest-asyncio, pytest-xdist, pytest-cov, moto, ruff, mypy, hatch,
  pre-commit, commitizen).
- `git` installed (used by hatch / general dev workflows).
- `SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0` is set because the project derives its
  version from git tags via `hatch-vcs`; the build context has no `.git`, so a
  pretend version lets the editable install succeed.

## Usage
```bash
cd phantom-stack
sudo docker build --network=host -f Dockerfile -t strands-sdk-python:dev ..
sudo docker-compose up -d
```

Exec into the container and run project commands:
```bash
# Collect tests (success criteria)
sudo docker-compose exec sdk-python python -m pytest --co -q

# Run the unit test suite
sudo docker-compose exec sdk-python python -m pytest tests -q

# Lint / format / type-check
sudo docker-compose exec sdk-python ruff check
sudo docker-compose exec sdk-python mypy ./src
```

## Verified
- `python -m pytest --co -q` → **1705 tests collected**
- `python -m pytest tests/strands/types tests/strands/tools -q` → **467 passed**
- `import strands; from strands import Agent` → OK
- ruff / mypy / hatch / pytest all available
