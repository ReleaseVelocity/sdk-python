FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the source may be shipped
# without git metadata, provide a pretend version so the build succeeds.
# This is configurable via build arg / env var.
ARG STRANDS_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=${STRANDS_VERSION}
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION}
ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# Build tooling for any native wheels (kept minimal).
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the project installs in editable mode
# and hatchling reads the package tree at build time).
COPY src ./src

# Install the project in editable/development mode with ALL optional extras
# plus the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
