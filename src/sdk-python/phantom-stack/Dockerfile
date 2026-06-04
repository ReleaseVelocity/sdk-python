FROM python:3.12-slim

# hatch-vcs derives the version from git tags. No .git is shipped, so we
# provide a pretend version via setuptools_scm (hatch-vcs backend).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# Minimal build tooling; only added because some transitive deps may build
# native wheels. Kept small.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-fetch dependencies using the manifest. The package itself is installed
# editable in a later step once the full source is present.
RUN pip install --upgrade pip

# Copy the full source tree.
COPY . /app

# Install the project in editable/development mode with ALL optional features
# and the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
