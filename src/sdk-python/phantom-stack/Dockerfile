FROM python:3.12-slim

# hatch-vcs (setuptools_scm) needs a version; no .git is present in this workspace,
# so provide a pretend version so the editable install can build metadata.
ARG PKG_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION} \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata build).
COPY src ./src

# Install the project in editable/development mode with all optional + dev deps.
# `all` covers every model/provider extra; `dev` adds pytest, ruff, mypy, moto, etc.
RUN pip install -e ".[dev,all]"

# Bring in the test suites.
COPY tests ./tests
COPY tests_integ ./tests_integ

CMD ["sleep", "infinity"]
