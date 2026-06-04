FROM python:3.13-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version to satisfy the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# System deps:
# - git: some tooling expects it; harmless and small
# - build-essential: in case any sdist deps need to compile native extensions
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the project is installed in editable
# mode and the build backend references src/strands).
COPY src ./src

# Install the project in development/editable mode with ALL optional features
# plus the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Bring in the tests and remaining project files.
COPY . .

CMD ["sleep", "infinity"]
