FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree provided to the
# build is not a git checkout, so we supply a pretend version to satisfy the
# build backend (overridable at build time).
ARG STRANDS_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION}
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Minimal build tooling: some optional deps (e.g. in the [all] extra) may build
# native wheels. git is required by hatch-vcs to introspect the build backend.
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        build-essential \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the project installs in editable mode
# and hatchling reads packages = ["src/strands"]).
COPY src/strands ./src/strands

# Install the project in editable/development mode with all runtime feature
# extras plus the dev/test toolchain (pytest, moto, ruff, mypy, ...).
RUN pip install --upgrade pip \
    && pip install -e ".[dev,all]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
