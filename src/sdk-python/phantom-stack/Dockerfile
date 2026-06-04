FROM python:3.12-slim

# Strands Agents Python SDK - development container
# Installs the package in editable mode with ALL extras + dev/test dependencies
# so tests, linters, and type checkers can be run inside the container.

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    # hatch-vcs (setuptools-scm) reads the version from git tags. The source
    # tree has no git history here, so pretend a version to allow the build.
    HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0

WORKDIR /app

# Minimal build/runtime tooling. git is needed by hatch-vcs; portaudio dev
# headers are required to build pyaudio (pulled in by the bidi extras).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install to resolve the
# package + for hatchling to find the wheel target).
COPY src ./src

# Install the project in editable/development mode with every optional
# dependency group plus the dev/test tooling.
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["bash"]
