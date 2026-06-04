FROM python:3.13-slim

# hatch-vcs derives the version from git tags. This repo has no git history in
# the build context, so provide a pretend version to satisfy the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first to leverage Docker layer caching.
COPY pyproject.toml README.md ./

# Pre-fetch the package source needed for editable install metadata.
COPY src ./src

# Install the project in editable/development mode with ALL optional features
# (model providers, a2a, otel, docs) plus the dev/test toolchain (pytest, moto,
# ruff, mypy, hatch, etc.). This makes every test importable and runnable.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
