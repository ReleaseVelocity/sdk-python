FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Source is copied without .git,
# so provide a pretend version (overridable at build time).
ARG STRANDS_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION} \
    HATCH_VCS_PRETEND_VERSION=${STRANDS_VERSION} \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    LOG_LEVEL=DEBUG

# System dependencies:
# - git: hatch-vcs build backend expects git to be available
# - portaudio19-dev / libasound2-dev: required to build pyaudio (bidi extra),
#   mirrors the project's CI for Linux.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        libasound2-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install of strands).
COPY src ./src

# Install the project in editable/development mode with all model providers
# plus dev/test tooling (pytest, moto, ruff, mypy, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[dev,all]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
