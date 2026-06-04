# Phantom Stack — Strands Agents Python SDK

Containerized development environment for the `strands-agents` SDK.

## What it sets up
- Base image: `python:3.13-slim`
- Installs the project in editable/dev mode: `pip install -e ".[all,dev]"`
  - `all` = every runtime model-provider extra (anthropic, gemini, litellm,
    openai, mistral, ollama, writer, sagemaker, a2a, otel, docs)
  - `dev` = full test/lint toolchain (pytest, pytest-asyncio/-xdist/-cov, moto,
    ruff, mypy, hatch, pre-commit, commitizen)
  - The `bidi` extra is intentionally excluded — it needs native PortAudio
    (pyaudio) and is excluded from the test suite & lint config in
    `pyproject.toml`.
- `SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0` is set because the build context is not
  a git checkout and `hatch-vcs` would otherwise fail to derive a version.

No external services are required: AWS interactions in the tests are mocked with
`moto`.

## Usage
```bash
# Build (host network required — the bridge network has no outbound access)
cd phantom-stack
sudo docker build --network=host -f Dockerfile -t app ..

# Start
sudo docker-compose up -d --no-build

# Collect tests
sudo docker-compose exec sdk-python python -m pytest --co -q

# Run the suite (parallelized)
sudo docker-compose exec sdk-python hatch test    # or: python -m pytest -n auto

# Lint / type-check
sudo docker-compose exec sdk-python ruff check
sudo docker-compose exec sdk-python mypy ./src
```

## Verified success criteria
- `python -c "import strands"` succeeds
- `pytest --co -q` collects 1705 tests
- A representative subset (`tests/strands/{test_async.py,types,tools}`) runs:
  **469 passed**
- `ruff`, `mypy`, `hatch`, `pytest` all available
