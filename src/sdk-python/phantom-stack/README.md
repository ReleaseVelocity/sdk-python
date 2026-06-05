# Docker setup for strands-agents (sdk-python)

This containerizes the Strands Agents Python SDK for development & testing.

## What it does
- Base image: `python:3.12-slim` (project requires Python >= 3.10).
- Installs the project in editable/development mode with **all** optional
  feature extras and the **dev** dependency group:
  `pip install -e ".[all,dev]"` — this provides pytest, ruff, mypy, moto,
  hatch, pre-commit, plus every model-provider integration (anthropic, gemini,
  litellm, openai, mistral, ollama, writer, sagemaker, a2a, otel, docs).
- `hatch-vcs` derives the version from git tags. The build context has no git
  metadata, so `SETUPTOOLS_SCM_PRETEND_VERSION` (build arg `PACKAGE_VERSION`,
  default `0.0.0`) is supplied to keep the install deterministic.
- The source tree is bind-mounted at `/app`, and the editable install resolves
  against `/app/src/strands`, so live code edits are reflected immediately.

## Usage
```bash
cd phantom-stack
sudo docker-compose up -d --build

# Open a shell
sudo docker-compose exec sdk-python bash

# Run the test suite
sudo docker-compose exec sdk-python python -m pytest -q

# Lint / type-check
sudo docker-compose exec sdk-python ruff check
sudo docker-compose exec sdk-python mypy ./src
```

## Verification (success criteria)
```bash
sudo docker-compose exec sdk-python python -c "import strands"
sudo docker-compose exec sdk-python python -m pytest --co -q   # 1705 tests collected
```

Notes:
- The `bidi` extra (PyAudio/PortAudio) is intentionally excluded — it is not
  part of `[all]`, and bidi tests are ignored by the project's pytest addopts.
- AWS test credentials are set via env (`AWS_ACCESS_KEY_ID=test`, etc.); the
  unit tests use `moto` mocks and never hit real AWS.
