FROM python:3.12-slim

# hatch-vcs derives the package version from git tags. The build context has no
# .git directory, so provide a pretend version to the build backend so the
# editable install succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# git is required by hatch-vcs at build time; keep build tooling minimal.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Layer caching: copy dependency manifest + readme (referenced by pyproject) first.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install with hatchling) and tests.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Install the project in editable/development mode with ALL optional model
# providers plus the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
