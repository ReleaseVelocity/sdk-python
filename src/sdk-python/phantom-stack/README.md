# Strands Agents Python SDK — Dockerized Dev Environment

The project at `/workspace/src/sdk-python` is the **strands-agents** Python SDK — a
pure library (no server/CLI daemon). The runnable, verifiable artifact is its
test suite, so this container is set up as a full development environment with all
dependencies (runtime + dev tooling + every model-provider extra) installed in
editable mode.

## What's inside
- Base image: `python:3.12-slim` (project requires Python >= 3.10).
- `pip install -e ".[dev,all]"` — editable install with:
  - `dev` extra: pytest, pytest-asyncio, pytest-xdist, pytest-cov, moto, mypy, ruff, hatch, pre-commit, commitizen.
  - `all` extra: anthropic, openai, litellm, gemini, mistral, ollama, llamaapi, writer, sagemaker, a2a, otel — the test suite imports these provider SDKs directly (matches the project's `hatch-test` env `features=["all"]`).
- The `bidi` extra (pyaudio/portaudio native deps) is intentionally **not** installed —
  the project's own pytest config (`addopts = --ignore=tests/strands/experimental/bidi ...`)
  excludes those tests by default.

### Versioning note
`hatch-vcs` derives the package version from git tags. The build context has no
`.git`, so `SETUPTOOLS_SCM_PRETEND_VERSION` / `HATCH_VCS_PRETEND_VERSION` are set
(default `0.0.0`) so the editable build resolves a version. Override via build arg:
`--build-arg PKG_VERSION=1.2.3`.

## Usage
```bash
cd phantom-stack
sudo docker-compose up -d

# Collect the full test suite (success criteria)
sudo docker-compose exec sdk-python python -m pytest --co -q

# Run tests (parallel via xdist)
sudo docker-compose exec sdk-python python -m pytest -n auto

# Lint / type-check
sudo docker-compose exec sdk-python ruff check
sudo docker-compose exec sdk-python mypy ./src
```

## Verified success criteria
- `import strands` succeeds.
- `python -m pytest --co -q` collects **1705 tests**.
- Sample test modules pass (tools, litellm model, agent state): **86 passed**.
- Dev tools available: ruff, mypy, hatch, pytest.
