FROM python:3.12-slim

# System deps: git (some tooling expects it), build tools only if needed for native wheels.
# portaudio is required only for the optional `bidi` extra (pyaudio); not installed here
# since the default/all feature set and the test suite exclude bidi.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        ca-certificates \
    && rm -rf /var/lib/apt/lists/*

ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# hatch-vcs derives the version from git tags. This source tree is not a git
# repo, so provide an explicit version to the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the install is editable and hatchling
# reads packages = ["src/strands"]).
COPY src/strands ./src/strands

# Install the project in editable/dev mode with all model providers + dev tooling.
# `all` => a2a, anthropic, docs, gemini, litellm, llamaapi, mistral, ollama,
#          openai, writer, sagemaker, otel
# `dev` => pytest, pytest-cov, pytest-asyncio, pytest-xdist, moto, mypy, ruff, etc.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
