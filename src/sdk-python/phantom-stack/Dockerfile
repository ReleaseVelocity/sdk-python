FROM python:3.12-slim

# git: hatch-vcs build backend invokes git; ca-certificates for TLS.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        ca-certificates \
    && rm -rf /var/lib/apt/lists/*

ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# hatch-vcs derives the package version from git tags. This source tree is not a
# git repository, so provide an explicit version to the build backend instead.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

WORKDIR /app

# Copy dependency manifests + package source first for better layer caching.
# hatchling reads packages = ["src/strands"], so the source must exist for an
# editable install.
COPY pyproject.toml README.md ./
COPY src/strands ./src/strands

# Install the project in editable/dev mode with ALL model providers + dev tooling.
#   all => a2a, anthropic, docs, gemini, litellm, llamaapi, mistral, ollama,
#          openai, writer, sagemaker, otel
#   dev => pytest, pytest-cov, pytest-asyncio, pytest-xdist, moto, mypy, ruff,
#          commitizen, hatch, pre-commit
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Bring in the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
