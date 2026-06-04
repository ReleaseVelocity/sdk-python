FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no git
# repository, so we pretend a version to satisfy the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install since hatchling
# validates the configured package path at install time).
COPY src ./src

# Install the project in editable/development mode with all optional features
# plus the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
