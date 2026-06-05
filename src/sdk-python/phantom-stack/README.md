# Phantom Stack — Strands Agents Python SDK (strands-agents)

Containerized development environment for the `strands-agents` Python SDK.
The project is a pure Python library with an extensive pytest suite, so the
container installs the package in **editable/development mode** with all
runtime extras (`[all]`) plus the dev/test toolchain (`[dev]`: pytest, ruff,
mypy, moto, pytest-asyncio, pytest-xdist, etc.). No external services are
required — AWS calls in tests are mocked with `moto`.

## Files
- `Dockerfile` — `python:3.12-slim` base; installs `pip install -e ".[all,dev]"`.
- `docker-compose.yaml` — single `sdk-python` service that idles (`sleep infinity`)
  so a developer can exec in and run tests/linters.

## Notes on building
The Docker bridge network used by `docker-compose build` has **no outbound
connectivity**, so the image must be built with host networking first, then
started with compose (which skips the build):

```bash
cd /workspace/src/sdk-python
sudo docker build --network=host -f phantom-stack/Dockerfile -t strands-sdk-python:dev .
cd phantom-stack
sudo docker-compose up -d --no-build
```

`hatch-vcs` derives the version from git tags. The build context has no `.git`,
so `SETUPTOOLS_SCM_PRETEND_VERSION` (via the `PACKAGE_VERSION` build arg,
default `0.0.0`) is supplied so the build succeeds.

## Verify
```bash
# Collect the full suite
sudo docker-compose exec sdk-python python -m pytest --co -q   # -> 1705 tests collected

# Run a subset
sudo docker-compose exec sdk-python python -m pytest tests/strands/tools/test_tools.py -q

# Dev tooling
sudo docker-compose exec sdk-python ruff check src/strands/agent/state.py
sudo docker-compose exec sdk-python mypy --version
```
