FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no git
# history, so provide a pretend version to satisfy the build backend.
ARG STRANDS_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION}
ENV HATCH_VCS_PRETEND_VERSION=${STRANDS_VERSION}

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

WORKDIR /app

# Upgrade pip tooling first.
RUN pip install --upgrade pip setuptools wheel

# Copy dependency manifest first to leverage Docker layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install) and tests.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Install the project in editable/development mode with all optional model
# providers plus the dev/test tooling (pytest, mypy, ruff, moto, etc.).
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
