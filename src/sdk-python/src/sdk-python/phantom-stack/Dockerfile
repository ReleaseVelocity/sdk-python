FROM python:3.12-slim

# hatch-vcs derives the version from git tags via setuptools-scm. The build
# context has no .git directory, so we provide a pretend version to satisfy the
# build backend. Both the generic and dist-specific vars are set for safety.
ARG PRETEND_VERSION=0.0.0+dev
ENV HATCH_VCS_PRETEND_VERSION=${PRETEND_VERSION} \
    SETUPTOOLS_SCM_PRETEND_VERSION=${PRETEND_VERSION} \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=${PRETEND_VERSION}

ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# git supports dev workflows (pre-commit, commitizen) and build-essential
# provides the compiler toolchain for any source dependencies.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Install the project in editable mode with all runtime extras plus dev/test
# tooling so a developer can run tests, linters, and type checks.
COPY src ./src
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
