# phantom-stack — Docker environment for `strands-agents` (sdk-python)

The **Strands Agents Python SDK** is a pure-Python library built with
`hatchling` + `hatch-vcs`. This setup containerizes it in **development mode**
with all optional features and the dev/test toolchain so you can run the test
suite, linters (`ruff`), and type checks (`mypy`) inside the container.

## What's installed
- Python 3.12 (`python:3.12-slim`)
- System build deps: `git` (for hatch-vcs versioning), `gcc` + `portaudio19-dev` (for `pyaudio`)
- `pip install -e ".[all,dev]"` — editable install with every model-provider
  extra (anthropic, gemini, litellm, openai, mistral, ollama, writer, sagemaker,
  a2a, otel, docs) plus dev tools (pytest, pytest-asyncio/cov/xdist, moto, ruff,
  mypy, hatch, pre-commit, commitizen).

The package version cannot be derived from git tags in this snapshot, so it is
provided via the `PKG_PRETEND_VERSION` build arg (default `1.0.0.dev0`), wired
through `SETUPTOOLS_SCM_PRETEND_VERSION`. A throwaway git repo+tag is also
initialized at build time for hatch-vcs.

## Build & run

The Docker bridge network has no outbound connectivity, so the image **must** be
built with host networking. Build the image first, then start with `--no-build`
(compose's BuildKit builder does not share the host-network cache):

```bash
cd /workspace/src/sdk-python
sudo docker build --network=host -f phantom-stack/Dockerfile -t sdk-python-app .

cd phantom-stack
sudo docker-compose up -d --no-build
```

## Verify (success criteria)

```bash
# Collect the full test suite (1705 tests)
sudo docker-compose exec -T sdk-python bash -lc 'cd /app && python -m pytest --co -q | tail -1'

# Run a slice of real tests
sudo docker-compose exec -T sdk-python bash -lc 'cd /app && python -m pytest tests/strands/types -q'
```

## Develop

```bash
sudo docker-compose exec sdk-python bash
# inside the container:
python -m pytest            # unit tests (tests/)
ruff check                  # lint
mypy ./src                  # type check
```
