# Docker environment for strands-agents (sdk-python)

This containerizes the Strands Agents Python SDK for development & testing.
The project is a Python **library** (no server/CLI daemon), so the container is
a long-running dev box with the package installed in editable mode plus ALL
extras and dev tooling (pytest, ruff, mypy, hatch, moto, ...).

## Build

The Docker bridge network has no outbound connectivity, so the image must be
built with host networking:

```bash
cd /workspace/src/sdk-python
sudo docker build --network=host -f phantom-stack/Dockerfile -t app .
```

## Start

```bash
cd /workspace/src/sdk-python/phantom-stack
sudo docker-compose up -d --no-build   # reuses the 'app' image built above
```

## Verify / Use

```bash
# Tests collect (1705 tests)
sudo docker-compose exec sdk-python python -m pytest --co -q

# Run a subset of unit tests
sudo docker-compose exec sdk-python python -m pytest tests/strands/types tests/strands/tools -q

# Dev tools
sudo docker-compose exec sdk-python ruff check
sudo docker-compose exec sdk-python mypy ./src
```

## Notes

- `requires-python >=3.10` → base image `python:3.13-slim`.
- Versioning uses `hatch-vcs` (git tags). The build context has no `.git`, so
  `SETUPTOOLS_SCM_PRETEND_VERSION*` env vars supply a fallback version `0.0.0`.
- The `bidi` extra (pyaudio/portaudio) is not part of `[all]`; portaudio dev
  headers are still installed for completeness.
