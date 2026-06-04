# Phantom Stack — Strands Agents Python SDK

Dev container for the `strands-agents` Python SDK (a library + test suite).

## What it does
- Base image: `python:3.12-slim` (project requires Python >= 3.10).
- Installs the project in **editable/dev mode** with every runtime extra plus
  the dev/test toolchain: `pip install -e ".[all,dev]"`.
- The project derives its version from git tags via `hatch-vcs`. The source
  here is not a git checkout, so `SETUPTOOLS_SCM_PRETEND_VERSION` (build arg
  `PRETEND_VERSION`, default `0.0.0`) is supplied to satisfy the build backend.
- Note: the `bidi` extra (pyaudio / aws_sdk_bedrock_runtime, py>=3.12 only) is
  intentionally excluded from `[all]` by the project itself, so it is not
  installed.

## Usage
```bash
cd phantom-stack
sudo docker-compose up -d

# Collect tests (success criteria)
sudo docker-compose exec sdk-python python -m pytest --co -q

# Run a subset
sudo docker-compose exec sdk-python python -m pytest tests/strands/types -q

# Full suite in parallel
sudo docker-compose exec sdk-python python -m pytest -n auto

# Lint / type-check
sudo docker-compose exec sdk-python ruff check
sudo docker-compose exec sdk-python mypy ./src
```

## Verified
- `import strands` succeeds.
- `pytest --co` collects 1705 tests.
- `tests/strands/tools` + `tests/strands/models`: 782 passed (with `-n auto`).
- Dev tools available: pytest, pytest-xdist, ruff, mypy, hatch.
