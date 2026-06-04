# Docker dev environment — Strands Agents Python SDK

Containerized development environment for `strands-agents`, installed in editable
mode with **all** optional features (`[all]`) and the full dev/test toolchain (`[dev]`):
pytest, pytest-asyncio, pytest-xdist, pytest-cov, moto, ruff, mypy, hatch, etc.

## Notes
- Base image: `python:3.12-slim` (project requires Python >=3.10).
- The project uses `hatch-vcs` for versioning from git tags. The build context has
  no git history, so a fallback version is provided via the `STRANDS_VERSION` build
  arg (`SETUPTOOLS_SCM_PRETEND_VERSION`). Override it if needed.
- Build context is the project root (`..`); the Dockerfile lives in `phantom-stack/`.

## Usage
```bash
cd phantom-stack
sudo docker build --network=host -f Dockerfile -t strands-sdk-python:dev ..
sudo docker-compose up -d

# Collect tests
sudo docker-compose exec sdk-python python -m pytest --co -q

# Run the unit test suite (parallel)
sudo docker-compose exec sdk-python python -m pytest -n auto

# Lint / type-check
sudo docker-compose exec sdk-python ruff check
sudo docker-compose exec sdk-python mypy ./src
```

## Verification performed
- `import strands` from the editable install at `/app/src/strands`.
- `pytest --co -q` → 1705 tests collected.
- `pytest tests/strands/tools -n auto` → 367 passed.
- ruff / mypy / pytest / moto all available.
