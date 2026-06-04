# Strands Agents SDK (sdk-python) — Dockerized Dev Environment

This stack containerizes the `strands-agents` Python SDK with **all** optional
model-provider extras and the full dev/test toolchain installed in editable
mode, so a developer can exec in and run tests, linters, and type checks.

## What's inside

- Base image: `python:3.12-slim` (project requires Python >= 3.10; test matrix
  targets 3.12/3.13).
- Install: `pip install -e ".[all,dev]"` — editable install with every model
  provider extra (`anthropic`, `gemini`, `litellm`, `openai`, `mistral`,
  `ollama`, `writer`, `sagemaker`, `a2a`, `otel`, `docs`) plus dev tools
  (`pytest`, `pytest-asyncio`, `pytest-xdist`, `pytest-cov`, `moto`, `mypy`,
  `ruff`, `hatch`, `commitizen`, `pre-commit`).
- The project uses `hatch-vcs` for versioning from git tags. The build context
  has no git history, so a pretend version (`SETUPTOOLS_SCM_PRETEND_VERSION` /
  `HATCH_VCS_PRETEND_VERSION`, default `0.0.0`) is supplied via build arg.

## Build & run

The Docker bridge network has no outbound connectivity, so build with host
networking first, then start the container (which reuses the prebuilt image):

```bash
# from the project root (/workspace/src/sdk-python)
sudo docker build --network=host -f phantom-stack/Dockerfile -t sdk-python:dev .

cd phantom-stack
sudo docker-compose up -d --no-build
```

## Verify

```bash
# package imports from the editable source tree
sudo docker-compose exec -T sdk-python python -c "import strands; print(strands.__file__)"

# collect the full unit-test suite (1705 tests)
sudo docker-compose exec -T sdk-python python -m pytest --co -q

# run a slice of the suite
sudo docker-compose exec -T sdk-python python -m pytest tests/strands/types -q
```
