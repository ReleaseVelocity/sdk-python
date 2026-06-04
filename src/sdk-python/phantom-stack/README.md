# Phantom Stack — Strands Agents Python SDK

Containerized development environment for `strands-agents` (the project at the repo root).

## What it provides
- Python 3.12 (project requires >=3.10)
- The package installed in **editable/dev mode** with the full feature set and dev tooling:
  `pip install -e ".[all,dev]"` (includes pytest, ruff, mypy, hatch, moto, etc.)
- `git` + `build-essential` + `portaudio19-dev` for native builds and hatch-vcs.

## Notes
- The project uses `hatch-vcs` for versioning from git tags. The build context here may
  not include a `.git` directory, so `HATCH_VCS_PRETEND_VERSION=0.0.0` is set so the
  build/install succeeds.
- The Docker bridge network has no outbound DNS, so the image must be built with host
  networking. The base image is pulled from `mirror.gcr.io` to avoid Docker Hub rate limits.

## Build & run

```bash
cd phantom-stack

# Build (host network required for apt-get / pip)
sudo docker build --network=host -f Dockerfile -t app ..

# Start
sudo docker-compose up -d
```

## Verify (success criteria)

```bash
# Package imports
sudo docker-compose exec -T sdk-python python -c "import strands; from strands import Agent, tool; print('OK')"

# Tests can be collected (1705 tests)
sudo docker-compose exec -T sdk-python python -m pytest --co -q

# Run the full unit suite
sudo docker-compose exec sdk-python python -m pytest
```
