FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs (setuptools-scm) needs a version; no .git is present in this build context
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Pre-resolve build backend
RUN pip install --upgrade pip hatchling hatch-vcs

# Copy the full source (needed for editable install of the package)
COPY . /app

# Install the project in editable/development mode with ALL optional features
# and the full dev/test toolchain (pytest, moto, ruff, mypy, hatch, etc.)
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
