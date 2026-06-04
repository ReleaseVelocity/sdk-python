FROM python:3.12-slim

# hatch-vcs derives version from git tags; no .git is shipped, so pretend a version
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest + readme first for layer caching.
# pyproject.toml references README.md, and hatchling needs the package source
# present to build in editable mode, so we copy the project metadata then source.
COPY pyproject.toml README.md ./

# Bring in source (needed for editable install build backend resolution)
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Install the project in editable/dev mode with all optional features + dev tools.
# "all" pulls every model provider; "dev" pulls pytest, ruff, mypy, moto, hatch, etc.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
