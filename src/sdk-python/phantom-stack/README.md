# Phantom Stack — Strands Agents Python SDK

Containerized development environment for `strands-agents` (the `sdk-python` project).

## What it is

The project is a pure Python library/SDK (build backend: hatchling, dev workflow: hatch).
There is no long-running server, so the container is verified through its test suite and
dev tooling — the way a developer/CI consumer actually uses the project.

## Image

- Base: `python:3.12-slim` (project requires Python >= 3.10, supports 3.10–3.13).
- Installs the project in **editable/development mode** with **all** extras plus the
  `dev` tooling group:  `pip install -e ".[all,dev]"`.
  This pulls in every model provider extra (anthropic, gemini, litellm, openai, etc.),
  a2a/bidi support, docs, and the test/lint tooling (pytest, pytest-asyncio,
  pytest-xdist, pytest-cov, moto, ruff, mypy, hatch, pre-commit, commitizen).
- System deps: `build-essential`, `portaudio19-dev` (for the bidi extra's `pyaudio`),
  and `git`.
- `SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0` is set because hatch-vcs derives the version
  from git tags, which are not present in this checkout.

## Usage

```bash
# Build (host networking is required for outbound package access)
cd /workspace/src/sdk-python
sudo docker build --network=host -f phantom-stack/Dockerfile -t app .

# Start
cd phantom-stack
sudo docker-compose up -d --no-build

# Exec in and work
sudo docker-compose exec sdk-python bash
```

## Verification (success criteria)

```bash
# Package imports
sudo docker-compose exec sdk-python python -c "import strands"

# Tests collect (1705 tests)
sudo docker-compose exec sdk-python python -m pytest --co -q

# Tests run
sudo docker-compose exec sdk-python python -m pytest tests/strands/types -q

# Dev tooling present
sudo docker-compose exec sdk-python sh -c "ruff --version && mypy --version && hatch --version"
```

## Note on build networking

The Docker bridge network has no outbound connectivity, so the image is built with
`docker build --network=host`. The compose file therefore references the pre-built
`app` image (via `image: app`) and is brought up with `--no-build`. The `build:` stanza
is retained so the image can be rebuilt with host networking when needed.
