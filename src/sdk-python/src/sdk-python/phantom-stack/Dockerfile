FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree has no .git,
# so provide a pretend version (setuptools_scm / hatch-vcs honor this env var).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# git is needed by hatch-vcs at build time; build-essential covers any
# packages that need to compile native extensions.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install to resolve the package).
COPY src ./src

# Install the project in editable/development mode with ALL optional features
# plus the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
