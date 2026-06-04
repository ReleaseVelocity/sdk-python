FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source is copied without a
# .git directory, so provide a fallback version to the build backend.
ARG PKG_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION} \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# Minimal build tooling; most wheels are prebuilt but some transitive deps
# (e.g. for a2a/sql extras) may need a compiler.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the project is installed editable and
# hatchling reads the package tree at build time).
COPY src ./src

# Install the project in editable/development mode with all runtime extras plus
# the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
