# Docker environment for strands-agents (sdk-python)

This containerizes the Strands Agents Python SDK for development and testing.

## What it does
- Base image: `python:3.12-slim` (project requires Python >=3.10).
- Installs the package in **editable mode** with the full dependency set:
  `pip install -e ".[all,dev]"` — all model providers plus dev/test tooling
  (pytest, pytest-asyncio, pytest-xdist, pytest-cov, moto, ruff, mypy, hatch).
- Provides a fake `SETUPTOOLS_SCM_PRETEND_VERSION` because the build context
  has no git history (the version is normally derived from git tags via
  hatch-vcs).

## Usage
```bash
cd phantom-stack
sudo docker build --network=host -f Dockerfile -t strands-sdk-python ..
sudo docker-compose up -d
```

## Verify
```bash
# Import works
sudo docker-compose exec sdk-python python -c "import strands; print('OK')"

# Collect the full test suite (1705 tests)
sudo docker-compose exec sdk-python python -m pytest --co -q

# Run a subset of unit tests
sudo docker-compose exec sdk-python python -m pytest tests/strands/types -q

# Dev tooling
sudo docker-compose exec sdk-python ruff check
sudo docker-compose exec sdk-python mypy ./src
```

Source is bind-friendly (editable install at `/app`); rebuild only when
dependencies change.
