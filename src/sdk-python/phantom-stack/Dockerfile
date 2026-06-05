FROM python:3.12-slim

# hatch-vcs derives the version from git tags. This source tree is not a git
# checkout, so provide an explicit fallback version for the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./
# The package source is needed by the editable install (hatchling reads it).
COPY src ./src

# Install the project in editable/development mode with ALL optional feature
# sets plus the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
