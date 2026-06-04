FROM python:3.12-slim

# hatch-vcs (setuptools-scm under the hood) derives the version from git tags.
# The build context here is not a git repo, so provide an explicit fallback
# version for the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# git is required by some dev tooling (pre-commit); build tools kept minimal.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for layer caching.
COPY pyproject.toml README.md ./

# Pre-resolve dependencies against the manifest (editable install of full
# project happens after source is copied).
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Install the project in editable/development mode with ALL optional feature
# sets plus the dev/test toolchain.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
