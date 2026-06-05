# Docker dev environment — Strands Agents Python SDK

Containerized development environment for the `strands-agents` SDK
(`/workspace/src/sdk-python`).

## What's inside
- **Base image:** `python:3.12-slim` (project requires Python >=3.10; the
  experimental `bidi` module needs >=3.12, so 3.12 covers everything).
- **Install:** the package is installed in **editable/dev mode** with every
  extra group: `pip install -e ".[all,dev,bidi]"`. This pulls in all runtime
  deps plus pytest, pytest-asyncio, pytest-xdist, pytest-cov, ruff, mypy,
  hatch, moto, etc.
- **Native build deps:** `gcc/g++`, `portaudio19-dev` (for the `pyaudio`
  dependency in the `bidi` extra), `libffi-dev`, and `git`.
- **Versioning:** the project uses `hatch-vcs` (setuptools-scm) which derives
  the version from git tags. The build context has no `.git`, so
  `SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0` is set to let the editable install
  compute a version without failing.

## Build & run
```bash
cd /workspace/src/sdk-python/phantom-stack
sudo docker build --network=host -f Dockerfile -t sdk-python:dev ..   # or: sudo docker-compose build
sudo docker-compose up -d
```

The build context is the project root (`context: ..`) so the whole source tree
is available for the editable install.

## Verify
```bash
# Import the package
sudo docker-compose exec sdk-python python -c "import strands; print('ok')"

# Collect the full unit-test suite (~1700 tests)
sudo docker-compose exec sdk-python sh -c "cd /app && pytest --co -q"

# Run the unit tests (parallel via pytest-xdist)
sudo docker-compose exec sdk-python sh -c "cd /app && pytest -n auto -q"

# Lint / type-check
sudo docker-compose exec sdk-python sh -c "cd /app && ruff check && mypy ./src"
```

Notes:
- `pytest` honours the project's `pyproject.toml` config (`testpaths=["tests"]`,
  bidi tests are ignored there per the project's own settings).
- Dummy AWS credentials/region are set in compose so unit tests (which use
  `moto`/mocks) never reach real AWS endpoints.
