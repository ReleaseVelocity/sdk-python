FROM python:3.12-slim

# hatch-vcs (setuptools-scm) derives the package version from git tags. The
# source tree is not a git checkout in this environment, so provide an explicit
# pretend version to the build backend to avoid version-detection failures.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Build deps occasionally needed by transitive native packages. Keep minimal.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install of strands-agents).
COPY src/strands ./src/strands

# Install the project in editable/development mode with ALL optional features
# plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
