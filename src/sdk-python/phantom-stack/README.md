# Docker dev environment — strands-agents (sdk-python)

This containerizes the **Strands Agents Python SDK** for development & testing.
It is a pure Python library (no server), so the success criterion is that the
full test suite can be collected and run inside the container, and that the dev
toolchain (pytest, ruff, mypy, hatch) is available.

## What's inside
- Base image: `python:3.12-slim` (project requires Python >= 3.10).
- The project is installed in **editable/development mode** with **all** feature
  extras and the **dev** toolchain: `pip install -e ".[all,dev]"`.
- `hatch-vcs` derives the version from git tags. Because the build context has no
  git history, `HATCH_VCS_PRETEND_VERSION=0.0.0+dev` is supplied so the build and
  editable install succeed.

## Build & run

The Docker bridge network has no outbound DNS, so the image must be built with
host networking. Build the image with the name docker-compose expects, then start:

```bash
cd phantom-stack

# Build (host networking required for apt/pip):
sudo docker build --network=host -f Dockerfile -t phantom-stack-sdk-python ..

# Start the container:
sudo docker-compose up -d --no-build
```

## Verify (success criteria)

```bash
# Collect the whole test suite (~1705 tests):
sudo docker-compose exec sdk-python python -m pytest --co -q

# Run a fast subset to confirm tests execute:
sudo docker-compose exec sdk-python python -m pytest tests/strands/types/ -q

# Dev tooling:
sudo docker-compose exec sdk-python ruff --version
sudo docker-compose exec sdk-python mypy --version
sudo docker-compose exec sdk-python hatch --version
```

## Exec in for development

```bash
sudo docker-compose exec sdk-python bash
```
