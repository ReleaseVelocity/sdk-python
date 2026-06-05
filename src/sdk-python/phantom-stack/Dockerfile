FROM python:3.12-slim

# Avoid interactive prompts, ensure consistent Python behavior
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1

# build-essential is included for any deps that need to compile native extensions.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# The package uses hatch-vcs to derive its version from git tags. This source
# tree has no .git directory, so provide an explicit version to hatch-vcs.
ARG STRANDS_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION} \
    HATCH_VCS_PRETEND_VERSION=${STRANDS_VERSION}

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source.
COPY src ./src

# Copy the test suites.
COPY tests ./tests
COPY tests_integ ./tests_integ

# Install the project in editable/development mode with ALL optional features
# and the full dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
