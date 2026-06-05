FROM python:3.12-slim

# Avoid interactive prompts and enable predictable Python behavior
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs derives the version from git tags. The source tree has no git
# metadata, so provide a pretend version to satisfy the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0 \
    HATCH_VCS_PRETEND_VERSION=1.0.0

# git is occasionally needed by build/test tooling; keep the layer minimal.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Upgrade pip tooling first
RUN pip install --upgrade pip setuptools wheel

# Copy dependency manifest first to leverage layer caching
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install to resolve the package)
COPY src ./src

# Install the project in editable/development mode with ALL extras + dev tools.
# This pulls in every model-provider dependency plus pytest, ruff, mypy, moto, etc.
RUN pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
