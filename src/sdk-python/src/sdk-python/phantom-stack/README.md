# Phantom Stack — Strands Agents Python SDK

Dockerized development environment for the `strands-agents` SDK (package `strands`).

## What it provides

- Python 3.12 (project requires `>=3.10`)
- The SDK installed in **editable/dev mode** with the full `[all]` runtime extras
  plus the `[dev]` test/lint toolchain (`pytest`, `pytest-asyncio`, `pytest-xdist`,
  `pytest-cov`, `moto`, `ruff`, `mypy`, `hatch`, `pre-commit`, `commitizen`).
- All tests collectable and runnable inside the container.

## Notes

- The project uses **hatch-vcs** to derive its version from git tags. This source
  tree is not a git repository, so a pretend version is supplied via the
  `PRETEND_VERSION` build arg (`HATCH_VCS_PRETEND_VERSION` /
  `SETUPTOOLS_SCM_PRETEND_VERSION`). Override it at build time if needed.
- The `bidi` extras (which need native `portaudio`/`pyaudio`) are intentionally
  excluded — they are not part of `[all]` and require audio system libraries.

## Usage

```bash
cd phantom-stack
sudo docker-compose up -d --build

# Exec in
sudo docker-compose exec sdk-python bash
```

## Verify (success criteria)

```bash
# Test collection (1705 tests)
sudo docker-compose exec sdk-python python -m pytest --co -q

# Run a sample of the suite
sudo docker-compose exec sdk-python python -m pytest tests/strands/types -q

# Lint / type-check
sudo docker-compose exec sdk-python ruff check
sudo docker-compose exec sdk-python mypy ./src
```
