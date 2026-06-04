FROM python:3.12-slim

# hatch-vcs determines the package version from git tags. This build context is
# not a git repository, so provide a pretend version for hatch-vcs/setuptools_scm.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# System build deps. git is handy for pre-commit / dev tooling; build-essential
# allows compilation of any source-only dependencies.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest + readme first for better layer caching.
# (README.md is referenced by pyproject.toml's readme field.)
COPY pyproject.toml README.md ./

# Package source is required for the editable install's metadata resolution.
COPY src ./src

# Install the project in editable/development mode with ALL runtime extras plus
# the full dev/test toolchain (pytest, pytest-asyncio, pytest-xdist, moto, ruff,
# mypy, hatch, commitizen, pre-commit, ...).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the remainder of the project (tests, tests_integ, configs, docs).
COPY . .

# Keep the container alive so developers can exec in to run tests/linters.
CMD ["sleep", "infinity"]
