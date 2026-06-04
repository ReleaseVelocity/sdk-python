FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version to satisfy the dynamic version metadata.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    LOG_LEVEL=DEBUG

WORKDIR /app

# System dependencies:
# - build-essential: native builds (pyaudio and friends)
# - portaudio19-dev, libasound2-dev: required by pyaudio (bidi extras)
# - git: hatch-vcs / general dev convenience
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        libasound2-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

# Upgrade pip tooling and install hatch (project's canonical dev/test runner)
RUN pip install --upgrade pip setuptools wheel hatch uv

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata resolution)
COPY src ./src

# Install the project in editable/development mode with ALL extras + dev tooling.
# This provides every model provider, a2a, otel, sagemaker, docs, plus the dev
# tools (pytest, ruff, mypy, moto, etc.).
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs, etc.)
COPY . .

CMD ["sleep", "infinity"]
