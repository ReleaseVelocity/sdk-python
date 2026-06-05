# Phantom Stack — Strands Agents Python SDK

Containerized development environment for the `strands-agents` Python SDK.

## What it does
- Base image: `python:3.12-slim` (project requires Python >=3.10)
- Installs the package in **editable/development mode** with all model-provider
  extras and dev/test tooling: `pip install -e ".[all,dev]"`
  (pytest, pytest-asyncio, pytest-xdist, pytest-cov, moto, mypy, ruff, hatch, ...)
- `HATCH_VCS_PRETEND_VERSION` is set so the hatch-vcs (git-tag) versioning works
  without git history.

## Build
The Docker bridge network has no outbound access, so the image must be built
with host networking:

```bash
cd /workspace/src/sdk-python
sudo docker build --network=host -f phantom-stack/Dockerfile -t app .
```

## Run
```bash
cd /workspace/src/sdk-python/phantom-stack
sudo docker-compose up -d
```

## Verify (success criteria)
```bash
# Package imports
sudo docker-compose exec sdk-python python -c "from strands import Agent; print('OK')"

# Full test suite collects (1705 tests)
sudo docker-compose exec sdk-python python -m pytest --co -q

# Run tests
sudo docker-compose exec sdk-python python -m pytest -q
```

## Dev commands inside the container
```bash
sudo docker-compose exec sdk-python ruff check
sudo docker-compose exec sdk-python mypy ./src
sudo docker-compose exec sdk-python hatch test
```
