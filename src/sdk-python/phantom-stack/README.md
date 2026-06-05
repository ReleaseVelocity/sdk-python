# Docker setup for `strands-agents` (sdk-python)

Strands Agents is a pure Python SDK/library (no server or CLI daemon). It is
containerized here as a **development environment** with the full dependency
set so a developer can exec in and run the test suite, linters, and type
checks.

## What's installed
- Base image: `python:3.12-slim` (project requires Python >= 3.10).
- The project installed in **editable mode** with every optional feature set
  plus the dev tooling: `pip install -e ".[all,dev]"`.
  This brings in pytest, pytest-asyncio, pytest-xdist, pytest-cov, moto, ruff,
  mypy, hatch, commitizen, and all model-provider extras
  (anthropic, openai, litellm, gemini, ollama, mistral, writer, a2a, etc.).

### Version note
`pyproject.toml` uses `hatch-vcs`, which derives the package version from git
tags. The build context has no `.git`, so `SETUPTOOLS_SCM_PRETEND_VERSION`
(and `HATCH_VCS_PRETEND_VERSION`) are set to `0.0.0+dev` to let the build
backend succeed.

## Usage
```bash
cd phantom-stack
sudo docker build --network=host -f Dockerfile -t strands-sdk-python:dev ..
sudo docker-compose up -d
```

## Verify (success criteria)
Collect all tests (1705 collected):
```bash
sudo docker-compose exec sdk-python python -m pytest --co -q
```

Run tests:
```bash
sudo docker-compose exec sdk-python python -m pytest tests/strands/types -q
# parallel full-ish run
sudo docker-compose exec sdk-python python -m pytest tests -q -n auto
```

Lint / type-check:
```bash
sudo docker-compose exec sdk-python ruff check
sudo docker-compose exec sdk-python mypy ./src
```

AWS-dependent tests are mocked via `moto`; dummy AWS credentials/region are
provided in the compose environment so boto3 never reaches real endpoints.
