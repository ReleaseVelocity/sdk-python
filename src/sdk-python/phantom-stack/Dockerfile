FROM python:3.12-slim

# Version is normally derived from git tags via hatch-vcs. Since the build
# context may not include a populated git history, provide a pretend version
# so hatchling can build the package in editable/development mode.
ARG STRANDS_VERSION=1.0.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION} \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# git is needed by hatch-vcs; build tooling for any native deps.
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (required for editable install with hatchling).
COPY src ./src

# Install the project in development/editable mode with ALL optional
# features plus the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

# Provide dummy AWS creds so boto3-backed tests/imports don't fail on import.
ENV AWS_DEFAULT_REGION=us-west-2

CMD ["sleep", "infinity"]
