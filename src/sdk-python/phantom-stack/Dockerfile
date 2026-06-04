FROM python:3.12-slim

# hatch-vcs derives the version from git tags. This checkout has no .git,
# so provide a pretend version for the setuptools-scm/hatch-vcs backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PYTHONUNBUFFERED=1

# Minimal build tooling; most wheels are prebuilt for slim.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir referenced by the build backend so the
# editable install can resolve packages = ["src/strands"].
COPY src ./src

# Install the project in editable/development mode with all runtime
# extras plus dev/test tooling (pytest, moto, ruff, mypy, hatch, ...).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
