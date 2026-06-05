FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no git
# history, so provide a pretend version to satisfy the version source.
ARG STRANDS_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION} \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Upgrade pip tooling first (better resolver, wheels).
RUN python -m pip install --upgrade pip setuptools wheel

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install build backend).
COPY src ./src

# Install the project in editable/development mode with ALL optional model
# providers plus the dev/test toolchain (pytest, moto, mypy, ruff, etc.).
RUN python -m pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
