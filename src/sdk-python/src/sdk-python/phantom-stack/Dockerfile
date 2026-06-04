FROM python:3.13-slim

# Avoid interactive prompts and enable clean Python output
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1

# hatch-vcs derives the version from git tags. This source tree has no git
# history, so provide an explicit version to the setuptools_scm backend used
# by hatch-vcs. This keeps the value configurable via build arg / env.
ARG PKG_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION} \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=${PKG_VERSION}

# Minimal build tooling: most wheels are prebuilt, but a few transitive deps
# may need a compiler. git is required by hatch-vcs at build time.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source needed for editable install (package + version source).
COPY src/ ./src/

# Install the project in editable/dev mode with all optional features plus the
# dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
