FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context has no
# .git metadata, provide a pretend version so the build does not fail.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

WORKDIR /app

# System deps: git (in case vcs is queried) and build basics for any native wheels.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the project is installed in editable mode
# and hatchling reads the package layout at install time).
COPY src ./src

# Install the project in editable/development mode with all optional features
# and dev/test tooling (pytest, ruff, mypy, moto, etc.).
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
